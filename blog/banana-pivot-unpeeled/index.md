# Banana Pivot: Unpeeled
Feb 29, 2024

On the 1st of February, we announced the pivoting of Banana, and the [sunsetting of our Serverless GPUs product](https://banana.dev/blog/sunset). 

Here’s a bit more about what happened, and a peek into what’s next for Banana, the startup.

### What Happened?

Banana built a highly demanded, well-loved Serverless GPU platform for developers to build their AI apps on. We served more than three thousand teams during its lifetime. 

But despite the hype, we experienced flat / down revenue through 2023. 

![Our revenue since launch](https://banana-dev.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F55445ebe-4d44-4adc-a538-1d70c744034f%2F54574ecf-2d5f-4b00-926f-8e244d47b6aa%2FScreenshot_2024-02-28_at_2.27.31_PM.png?table=block&id=9ae81394-e92c-4942-b047-f9d1193af2eb&spaceId=55445ebe-4d44-4adc-a538-1d70c744034f&width=960&userId=&cache=v2)

Our revenue since launch

We had a constant stream of qualified signups, the closest to Product Market Fit I’ve ever experienced. User churn was the obstacle, due to pricing, latency, and reliability. All solvable things!

I held out hope for the entire year, trying my best to keep the team in good spirits. My conviction was that there was no single silver bullet to this product, just thousands of lead bullets.

As the year went on, and our work wasn’t bearing fruit, I began feeling deep shame that we were somehow dropping the ball. What an idiot I must be, catching lighting in a bottle then letting it slip away.

Second half of 2023 was a dark time. 

I stopped going into [f.inc](http://f.inc) because I was too ashamed to admit things were going poorly. I gained weight. I stopped seeing friends. I lost my ability to have fun. My cofounder and I gracefully parted ways. My wife and I nearly did too. I was just so stressed, so burnt out.

I didn’t feel great.

### The final months

In December 2023, I finally began accepting the fate of Banana’s serverless product, and mourning it. My mental health started recovering, lifted up by frequent gym trips, my cofounder, my team, and my friends. Above all, my wife, who through this process has shown me what marriage is really about. 

I was back on my feet, and ready to pick up the pieces of Banana. At this point we had a brand people remember, and an engineering team with battle scars from iterating on the the platform. But our runway was draining fast.

So I dedicated my time to finding a soft landing via acquisition. 

We’d been approached before, so I was confident I could drum up interest and get an offer. We talked to a dozen teams, whittled it down to five who were actually serious enough to start talking terms, and by February we had two initial offers. Neither were enough to return investor money, but enough to put decent cash in my own pocket and an “acquired” badge in my Twitter bio.

Great news, right?

I tried very hard to convince myself it was an acceptable outcome. But it didn’t feel right. I’ve been building this startup for more than five years, and have accumulated a cap table of more than a hundred people who believed in me. We still had a good chunk of money in the bank, equivalent to 9 months of runway if we cut burn wisely.

It came down to a critical day in one of the deals, and it was time to decide. I turned down the terms, and that was the end of it.

We’re not done yet. 

# What’s Next

Starting Feb 1 after the sunset announcement, we had the luxury of picking what’s next.

### Week 1: Ideation and the start of Fructose

At Banana, we observed a few critical things about the market through 2023

1. Most teams don’t need to host a custom model. Multi-tenant APIs generally get the job done, and if not, specialized finetuning services on common architectures get you the rest of the way.
2. LLMs, specifically text generation, appear to be the closest emulation of intelligence.
3. LLMs are becoming commoditized. Thanks to the OpenAI SDK becoming the standardized interface, users can port between GPT-4, Claude, Llama, Mixtral, and more, with a single line code change. 
4. It feels like most of the products being built involve presenting generated text to a human. Chatbots, search, code-gen, etc. The output of the model is still treated as a “string”. 
5. `code → LLM → code` interactions are less common, because LLMs are not deterministic and you can’t guarantee that the output follows a specific format, so more complex programs can crash if they receive an unexpected output. OpenAI has introduced json output in an attempt to make it more machine parsable, but even that’s not 100% guaranteed to match the target schema.

The magic of programming and open source is that we can build highly complex applications by composing functions together. 

LLMs in 2024 have gotten to a level of “intelligence” that they can perform data reasoning / transformation / creation that cannot reasonably be expressed with code.

Right now, most of us are just going one level deep, code calling LLMs and then presenting that to a human. But LLMs can represent more than a text generation; they can be a **function within a program.** And given 100% guaranteed interface, you can build highly complex applications, with code interweaving with LLMs, interweaving with code. 

Banana is building a guaranteed type interface around LLMs

We’re starting with Fructose:

https://github.com/bananaml/fructose 

Fructose is a python package to program with LLMs as strongly typed functions.

```python
from fructose import Fructose
ai = Fructose()

@ai()
def get_answer() -> int:
	"""
		Return the answer to life, the universe, and everything
	"""

answer = get_answer()
print(answer)       # -> 42
print(type(answer)) # -> int
```

The `@ai()` decorator turns the unimplemented function into an LLM call, to perform the given task. It uses the type signatures to guide the generation and guarantee a correctly typed output, in whatever basic/complex python datatype requested.

As you can see, you call it as if it were an implemented python function. 

Taking it a step further, just as your implemented functions can call other functions, fructose AI functions can do the same.

```python
from fructose import Fructose
import requests

ai = Fructose()

# fructose supports complex types
@dataclass
class Comment:
	author: str
	body: str

# a locally python function
def get(url: str) -> str:
	return requests.get(url).text

# the uses argument allows the ai function to call local functions
@ai(uses = [get])
def get_hackernews_comments() -> list[Comment]:
	"""
		Fetch the top 5 comments from the current top HackerNews post
	"""

comments = get_hackernews_comments()

print(len(comments)) # -> 5

for comment in comments:
	print(f"Comment: {comment.body}") # -> "Comment: This fructose package is trivial and could be implemented in less than 100 lines of code. What's the big deal?"
```

We spent the first week of February building this package. We launched privately to our discord and select twitter accounts, for initial feedback.

### Week 2-3: Building Fructose projects

After our launch, it was clear that there are parts of the library’s API that need to be rethought, and improved. It fell into the category of “this is cool” but for some, the wrapper was too thin, and for others, the wrapper was too abstracted.

I even tweeted that we were fully ditching it, in the spirit of leaving the “keep on chipping away” attitude that plagued us during Serverless GPUs. But I quickly decided I was too excited about the idea to not give it a proper swing.

The next two weeks were spent building projects on Fructose, to get a better feel for it. We also explored similar packages (Instructor, Marvin) to get a sense for what feels good and what feels bad.

Worth noting that we don’t intend to compete with existing tools. Fructose is, in our opinion, the most python-feeling interface to this concept, but regardless of the SDK users end up using, we started working on a harder challenge:

### Week 4: Hosted Formatting Model

99% reliability on each call is not acceptable for highly complex programs. We need 100%.

As an API consumer, you cannot guarantee with 100% certainty that the API returns the requested type because you’re not running the inference itself; you just get the completed string at the end. Client-only libraries such as Instructor, Marvin, and Langchain rely on prompt wrapping, multi-stage calls, and retries on parse failure, which adds a token burn and latency. 

We, having ran large model inference for thousands of teams, feel uniquely positioned to build a narrowly-scoped, highly efficient, hosted model that guides generation on a token-by-token level to match the needed type format.

We gathered our normally remote team together for an emergency work offsite over this last week, and this is what we emerged with.

![Banana team offsite](https://banana-dev.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F55445ebe-4d44-4adc-a538-1d70c744034f%2Fe942fbda-ab71-4464-a600-ab5ae94ea283%2FIMG_2880.jpeg?table=block&id=9b1f93ba-af47-4808-ac75-8c79a92415ec&spaceId=55445ebe-4d44-4adc-a538-1d70c744034f&width=580&userId=&cache=v2)

Banana team offsite

We’ve achieved successful structured extraction on basic examples, using arbitrarily complex python types as the return. This model has been deployed, and we’re stress testing it at this moment, and trying out different architectures as we find holes.

Large models like GPT-4 should be used for achieving your function’s goal, like performing chain of thought or deciding on tool calls. We want to give you the means to do that, but when it’s time for the `@ai()` function to resolve, we’ll run it through a lightweight formatting model that has a 100% formatting guarantee. 

Our loose plan is to offer this as an OpenAI proxy endpoint, so you can optionally point Fructose toward it to get 100% type guarantees and faster/cheaper responses.

### Week 5+:

At the time of writing, we’re in week 5! We’ve just returned from offsite, and jetlag is wearing off.

Moving forward, our priorities are:

- Fructose adoption / feedback. Iteration on package API.
- Continued R&D on the Formatting model
- Development of proxy and integration with fructose
- Reducing burn
- Increasing good vibes

We’re still exploratory. There are plenty of tools we’re aware of, and many more we’re certainly not aware of, so if you’re working in this space or are a potential user with strong opinions, I’d love to chat ([DM me](https://twitter.com/erikdunteman)).

You can use Fructose in its current state here:
https://github.com/bananaml/fructose