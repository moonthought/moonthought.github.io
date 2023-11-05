+++
title = "True readability doesn't exist! But you'll feel when it really is"
date = 2023-10-28T17:06:21+03:00
draft = false
+++

The question of what code readability is (as part of mystical DX in general) is often raised lately. And more and more people have started to say that readability is purely a matter of preference. I can't blame them. After many years of development, we may catch ourselves thinking that we are already quite capable of perceiving different code of various quality, paradigms and languages. We can also get used to tools that at first seemed uncomfortable to us. And conclude that everything depends on experience! Well, yes and no.

## It's a double-edged sword

[Tsodin](https://twitter.com/tsoding) recently said:

> There are two ends to this quality — how truly readable and maintainable the code is, and how skilful the programmer is

And you know, I'm somewhat comfortable with that opinion. And it could have stopped there, but later Tsodin pulled the blanket more on the programmers themselves and reduced everything to a generalised "git gud."

The problem with this statement is that we're sort of implicitly saying - any code is readable, you just don't have enough skills! And let's be honest: not all code is readably on it's own.

## Oh, but can you define what readability is?

You may often hear the question: «Can you define readability? No, no, not just readability, but code readability, that's quite different ya know.»

Indeed, readability is by definition divided into the polygraphic performance of the text and the linguistic features of the textual material. But we're not here to evaluate the contribution of font or ligatures to code readability, right?

What *would* really interest us is the linguistic aspect of the code:
* namings
* the complexity of syntactic constructs
* the level of abstraction
* etc.

And, again, this is all more applicable to the text, not the code. But there is one important difference between code and text: code, unlike text, is obliged to fulfil its intentions. It is not so important whether the text fulfils its intended purpose in, for example, conveying the author's intent. If it doesn't, well, it's just a bad text. But the code **must** solve the intended problem, otherwise it is literally wrong. Bad ≠ wrong.

Which may lead us to believe that code readability is, in fact, a composite phenomenon. And code readability is both a correct solution of the task at hand - where the code fully describes the working algorithm, i.e. it literally does not lie to us about its actions - and readability of the solution itself. Um, wait a sec, it's a bit of a tautology here!

## Code readability is not what you think it is

Notice how we switched from «code readability» to «solution readability?» I believe that this is the main feature of the problem of characterising readability: we are trying to look at the term without contextualisation.

Now, let's take a look at the really simple (mb too simple) examples:

```ruby
def calculate(bottom, top)
  return 0 if top < bottom

  sum = 0

  for number in bottom..top do
    next unless number % 2 == 0

    sum += number
  end

  sum
end
```

And

```ruby
def calculate(bottom, top)
  (bottom..top).select(&:even?).sum
end
```

What code is more readable? Some of you will say that both are quite readable. But are they really? As much as you might want to, the first option unequivocally contains more cognitive load than the second one. The second option you can literally read from left to right and understand the intent of the code. In the former, you have to switch both your eyes and context from one section of code to another. At the same time, you have to remember to filter the values in your head every time and also remember the top-level condition.

I'm not trying to say that short or functional-ish code is better. And yes, these examples look like not that big of a deal. But in the first case, we force the developer to proofread code that is only indirectly relevant to solving the problem. Early return by condition? That's an implementation detail, it has nothing to do with the problem being solved. Declaring a variable with a default value? Again, what does this have to do with our problem? A condition inside a loop? Again a detail. Even a loop itself is just a detail. And the more such details there are in the code, the less readable it may become (and most likely will.)

Now, some of you might say that it all depends on paradigm, or developer's experience, or generally that that's the point of programming! But you're confusing two different things:
* inherent complexity
* incidental Complexity

## What complexity?

Let me just quote:

> Inherent complexity — the difficulty of the actual problem that the software in question is trying to make simpler and better.

> Incidental complexity — anything about software that is hard but doesn’t really have to be

Incidental complexity is introduced by libraries, frameworks, languages, paradigms, developers's preferences etc. Inherent complexity is the struggles you have to make to solve fundamental problems of the chosen domain. A good engineer will reduce the Incidental complexity and try to accept and deal with the inherent complexity.

So no, if you can easily understand code with #ifdefs nested every 5 lines, or you're extremely good at reading ruby metaprogramming with delegating class members to an abstract context with evals — it doesn't mean that this code is readably. It's you who have learnt to read a pile of incidental complexities. And, in general, there is nothing wrong with it - there is an incredible amount of such code around, and it is better to be able to understand it than not.

## So it's not a matter of preference?

Why not? After all, whether the code will be readable for you personally also depends on your experience and preferences.

But your preferences in no way affect the complexity and readability of the solution. For example, a problem in which you need to implement cache clusters with shards and the ability to reshard it by size and quantity (which will lead to items redistribution) based on jump consistent hashing. And as the cherry on the cake - on the fly, no downtime, with cache snapshots, correct eviction policies and high hit miss ratio. The field itself is complicated enough as it is, why add more complexities and problems? I mean, it's inevitable to some extent. But only to **some** extent. But please stop justifying hard (*not* complex) code by preferences, experience and stuff.

It's all about how readable the solution is.