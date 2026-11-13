+++
draft = true
date = 2026-11-13T15:35:16-06:00
title = "Memory needs a waiting room"
description = "An AI memory provider ran out of credits, and I found the gap between keeping a database and keeping the work needed to update it."
authors = ["Zack"]
categories = ["Homelab"]
tags = ["ai", "memory", "queues", "self-hosting", "qdrant"]
disableComments = true
+++

I thought I had the important part of AI memory covered because the memory database was running in my lab. The data was there, the service was available, and I could point at the collection and say, yes, that is mine.

Then the OpenAI API credits ran out and I found the part I had not really accounted for. The existing database was still there. The trouble was getting new things into it.

That distinction sounds obvious now. It was less obvious when I was looking at a healthy local database and a memory system that was quietly failing to capture new material. Keeping what was already stored and making sure a new capture gets another chance are different jobs.

I ended up changing providers and migrating the stored vectors, but the more interesting change was a small SQLite queue in front of the memory work. The database had a place to keep memories. The capture process needed a waiting room.

## The database was not the thing that ran out

The setup used Mem0 to extract useful memories from conversation and store them in Qdrant. Qdrant was local. The extraction and embedding work depended on an external provider. When the API credits were exhausted, the local vector database did not disappear or suddenly lose its existing points. The provider-dependent part could not complete its work.

That is a pretty uncomfortable failure mode because a service can look alive while the thing I actually care about has stopped happening. A running database is not proof that new memories are being captured. A successful conversation is not proof of that either.

The capture attempts were failing or being dropped instead of sitting somewhere durable to be retried. Once the provider problem was fixed, there was not automatically a reliable list of every missed capture waiting to go back through. I needed to separate accepting the work from actually completing the provider call.

I am not going to put real conversation text in this post. The details of a conversation are not necessary to explain the problem, and logging somebody's private words to prove that memory works would be a pretty bad way to solve it.

## A queue before the expensive part

The new path records the pending turn in SQLite before dispatching the capture. Each item has an ID and enough routing information to keep the session, user, agent, and channel straight. Then a scheduled worker runs every five minutes and processes batches for one matching route at a time.

That is the basic shape. The conversation can produce work for memory without requiring the external provider to be healthy at that exact moment. If the process or provider has a temporary problem, the pending item is not supposed to vanish just because the first attempt did not finish.

The route matters more than it might look like in a small diagram. If I put everything into one pile and batch it without checking where it came from, the retry system could become a very efficient way to mix contexts that should stay separate. The queue needs to know what belongs together, not just that some text is waiting.

The IDs matter too. They give the system something concrete to track besides an informal idea that a conversation probably got processed. I can inspect the state of the work without dumping the actual conversation into the logs. The logs are about the queue and processing state, not a transcript of private material.

A five-minute schedule is not instant memory. That is a trade I am willing to make here. The point is to keep the work available for a later attempt, not to pretend the provider is synchronous and infallible. There is also a difference between an item being accepted into a queue and a usable memory being extracted from it. The first is a local state change; the second still depends on the rest of the pipeline doing its job.

## Retry is not a magic button

My first instinct with a failed operation is usually to run it again. That works nicely when I know the operation failed before it could write anything. If a request never got to the remote side, retrying it has a pretty understandable outcome.

The awkward case is when the request might have made it to the remote side but the response did not make it back. Did the write happen? Did it happen partially? Did the service finish after my timeout? From the local end, those possibilities can look a lot alike.

Blindly treating every timeout as a clean failure can turn recovery into duplicate work or duplicate effects. On the other hand, deleting the queued item because the request *might* have succeeded can lose the capture. Neither answer is particularly comforting.

So the queue distinguishes a failure known to be before a write, which can be retried, from an ambiguous remote effect, which is held rather than automatically replayed as if nothing happened. A held item needs attention or a safe way to resolve its state. That is less satisfying than a green dashboard saying everything will retry forever, but it is more honest about what the system knows.

This is an at-least-once approach, not an exactly-once guarantee. Retrying work can mean it is attempted more than once, and holding an ambiguous item means it is not automatically resolved just because the scheduler runs again. I would rather have that boundary visible than claim that a local queue somehow controls what happened on the other end of a network request.

SQLite is also not a force field. It gives the pending work a durable local place to live, subject to the health of the machine, disk, and database. It is not an offsite backup, and it does not prove that every possible crash or provider failure has been handled. It addresses the gap I found: work should not simply evaporate because the provider was unavailable when capture was attempted.

## Moving the memory that was already there

The queue was only part of this project. The existing memory still used embeddings from the old provider, and I was moving extraction and embeddings to Gemini while keeping Qdrant local.

I could not just call a different embedding model and assume the old and new vectors meant the same thing. Embeddings from different models live in different vector spaces, and even their dimensions may differ. Keeping the old vectors and querying them as if they were made by the new model would not be a migration I could trust.

The migration rebuilt the vectors with the new embeddings while preserving the point IDs and payloads. One collection had 4,114 IDs and payloads carried over. There was a second profile to migrate too; the important part is that both collections needed their own accounting rather than assuming one successful run covered everything.

I kept the old OpenAI-backed collection instead of destroying it as soon as the new one existed. That gave me a reference and a rollback option for the collection, not a complete backup strategy. Preserving an old collection is useful, but it does not cover a failed disk or prove I can restore every part of the system.

There were a couple of good opportunities to make the work harder than it needed to be. I initially treated an HTTP Qdrant restriction as if it were a real limitation of the setup. It was a restriction I had imposed, not a feature Qdrant was missing. Once I recognized that, I could stop designing around a problem of my own making.

I also got the first Gemini batch request wrong by sending a list of strings in the wrong shape. That produced a mismatch rather than the clean batch of embeddings I wanted. Fixing the request format was a much better use of time than deciding the entire provider switch was impossible. It is a familiar kind of mistake: I had a reasonable idea of what the API should accept, and the API had a more specific opinion.

The actual migration was not just changing a model name in a config file. It meant checking what was being moved, preserving identity and payload, rebuilding the vectors, and keeping the old collection around while I checked the result. Counts and IDs tell me something important about the transfer. They do not, by themselves, prove that every future question will retrieve the right memory.

## Local storage does not make this offline

It would be easy to describe this as moving memory local because Qdrant and the queue are on my side. That would leave out the part where Gemini receives the text needed for extraction and embeddings. The storage location and the processing location are different privacy decisions.

If I send private text to a hosted model, I am trusting that provider with it under whatever terms and settings apply to that service. Putting the resulting vectors in my own database does not undo that. Anyone building something similar should make that decision deliberately rather than assuming a local vector database keeps the entire pipeline on the local network.

There is a money boundary too. API usage is its own thing; it should not be assumed to come with a consumer subscription to a provider's chat product. Changing providers changes the dependency and the billing arrangement, but it does not remove the possibility of exhausting a quota, losing access, or hitting another service failure. I am not calling this a hard spending limit or a guarantee that the next bill will be small.

That was part of why the queue mattered even after the provider change. If the only fix had been swapping credentials or moving to another API, I would still have the same architectural problem the next time an external dependency stopped cooperating. The provider switch got the pipeline moving again. The queue gives pending work somewhere to wait when moving again is not immediate.

## Where it sits now

After the provider repair, a real queued capture was processed. That is a useful check: the system did more than create a table and congratulate itself for having a queue. It does not prove end-to-end recall for every kind of conversation, prove exactly-once behavior, or prove that all recovery and backup cases are covered.

I am also not treating the old failed captures as magically recovered. The point of adding durable tracking is to change what happens going forward, not to invent a list of work that was never reliably saved in the first place.

The lesson for me was not that Qdrant failed, or that one provider is immune to the problem that hit another. The local database kept doing its part while a separate dependency stopped the path into it. I had been treating those parts as one thing because, when everything was healthy, they felt like one thing.

Now the capture has a place to wait, a route that says where it belongs, and a state that can say "I do not know" when a remote operation has an ambiguous result. That is not a perfect memory system. It is a much better answer than assuming a healthy database means the work got there.
