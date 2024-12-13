---
title: Building a full-stack ML-backed app in 2024
date: 2024-12-09
tags: [misc]
use_math: true
---

# Introduction
Today, I want to talk about a recent personal project. In doing the project, I'd say I was probably 60% motivated by learning about new
techniques and tools that are out there, and 40% with seeing what it would be like to put together an end-to-end application with the help
of AI assistants, both within my coding environment (Github Copilot) and without (Anthropic's Claude with a smattering of OpenAI's ChatGPT). The
goal was not to build the most optimized, low-latency, state-of-the-art tool; it was to see if I could get all the pieces working together to do
something interesting.

I've called what I've built ML Paper Explorer. It is a simple interface that allows the user to search and save academic papers on machine
learning. The project required me to complete tasks in four broad categories: frontend, backend, machine learning (ML), and deployment. Below, I'll
talk about some of the features I built, what the experience of leaning heavily on AI was like, and close with some broader reflections on being a software engineer in this new age.

If you want to check the project out, go to [ml-paper-explorer.com](https://www.ml-paper-explorer.com).

# Features
I started with a few features I thought were interesting:
* **Paper relevance engine**: The key backend component was a machine learning engine that could search for papers relevant to a user's query. I implemented a two pass ranking system. The first uses a fast, keyword-based (non-ML) algorithm called [BM25](https://en.wikipedia.org/wiki/Okapi_BM25) to filter tens or hundreds of thousands of papers down to ~1000. To further filter the smaller collection down to what we show the user (on the order of 10), we use a text embedding model to generate a numerical, semantically rich representation of the user query and find the ~10 papers whose representations (which we've pre-stored in a vector database) are the "closest" to it.
* **Personalization**: Users can log in (no password required) to like papers and get recommendations based on what they've liked.
* **Explanations**: When a paper is returned in response to a query, the user can see an explanation of why the paper might have been returned in response to the query. This is simply implemented using some prompt engineering on top of the query and title/abstract of the paper in question.

(As a general note, when I talk about papers here, I'm just using titles and abstracts so as to keep storage and processing times reasonable.)

# Implementing the web app
Given my background, this part was quite unfamiliar to me, and to do it, I leaned _heavily_ on Anthropic's Claude.

Claude essentially wrote the first iteration of the frontend completely from scratch. Then, when I decided I wanted separate the initial one-page design into multiple pages, it deftly refactored the code to cleanly handle the updated organization. It even added a very aesthetic landing page without my explicitly asking for it! There were other small, probably underspecified changes I wanted to make, such as displaying a user's login status more cleanly, adding subtle animation when search results appear, changing the formats of the cards containing paper details, or adding a navigation bar, and it was able to correctly and efficiently make those changes as well. Implementing the backend felt a little bit more comfortable and familiar, but Claude was very helpful setting up some boilerplate code that I found straightforward to extend. As I'm sure other programmers have experienced, Claude was also very useful as a debugging partner.

Deployment with modern cloud-based tools is also not an area in which I'm terribly knowledgable or adept. In order to be fully operational online, I had to deploy:
* The backend
* The frontend
* A database to hold information about paper metadata and users' liked papers
* A vector database for paper similarity search

Enter Claude once again! It identified available services I could use to host the various pieces (though I ended up hosting the frontend with AWS Amplify rather than Vercel) and then helped me stumble my way through getting all of the pieces to connect and talk to one another. It helped me navigate some of Amazon's web interfaces by looking at screenshots, and also interpreted and explained various error messages that would have taken me longer to resolve on my own.

I should stress here that without Claude's help, even as a full-time software engineer, this same project that took me a few short weeks to get off the ground would have likely taken me several months, if not much longer.

# Reflections
While it was exciting to get something up and running so quickly, doing this project has sparked a few thoughts about what the emergence of tools as powerful as these models might mean for me as a software engineer going forward.

First, it seems less and less important than ever for information to live in my head. Not so long ago, to build this simple application I've described, I would have had to have reasonable command of web development, dev ops (for deployment), and machine learning fundamentals. I was able to get by with little-to-no up-to-date knowledge about _two out of three_ of those. Would I have been able to do this same project without any programming knowledge whatsoever? I'm not sure we're there yet. But I _was_ surprised how little I needed to get started.

With generative AI looking like it will mediate more and more parts of our work lives, it seems much more important to be able to articulate what you're looking for than to have a lot of pre-loaded a priori knowledge. Another way of saying this is that our ability to accomplish nontrivial things seems to be decorrelating from the amount of time we've spent learning about them. Given how much time I've spent studying computer science, math, and machine learning over the last decade, I find this unsatisfying! While I still believe that at this stage, deep understanding and investigation still helps me produce my best work, will that be the case if these models keep on improving at this pace?

The second thing that occurred to me is the way that a certain attribute of generative AI tools that disappoints some people makes it excellent as a coding partner. When people prompt AIs for things like essays and poems, their complaint is often that it's too... well... average. "That essay would get a B+," they say, "but I certainly wouldn't give it an A." I think some people's disappointment about the quality of AI writing can be boiled down to the fact that it feels sterile and derivative. As a programmer, this "average" quality is actually exactly what you want! When asking an AI for help with programming, what you're looking for is often the consensus opinion about the best way to solve this or that problem. The sort of averaging or convergence that occurs when you compress the entire internet into the parameters of a language model ends up making models like Claude and GPT very helpful programming partners, and frustratingly boring writers. (I think it's certainly possible to get AI to write things that are interesting, but it usually takes some effort and clever prompting.)

While I am admittedly somewhat uncomfortable about the ways that software engineering is going to change on a shorter timeline that I thought it might, I do believe that humanity will figure out how to leverage these AI technologies to create a better world. In the near-to-medium term, we will have to be extraordinarily careful about ethically applying them (or not) to sensitive areas like education, the military, biomolecular design, or our financial system, but if we can navigate those challenges successfully, I sincerely believe there is tremendous potential.

It's very possible, even likely, that in that new and hopefully improved world, you'll find me with an old computer, disconnected from the internet, coding, unassisted, like we did in the before times.