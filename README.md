## Road To Prompt Reliability Engineering
### Introduction
This article introduces the concept of Prompt Reliability Engineering as the final step on the prompt-ops maturity ladder. We will walk up this ladder and talk through the concepts and best practices on each of the steps. If you just deployed your first genai app and you're starting to get overwhelmed scrolling through the application logs or if prompt engineering  seems like taking one step forward and two steps back then this article is for you.

### Prompt-ops maturity levels
#### Yoloing prompts
This is where every project starts and many end. If you update a piece of text in VScode, hit save ,and then go on to refresh a web-page to try the changes in a chat app, then you're likely in this stage. You're hopefully using github or some type of source code versioning to save the changes to your prompts. Bonus points if you've set-up some type of ci/cd workflow that also deploys changes commited to your production app.
#### Logging & tracing
Once the llm app becomes more sophisticated and the developer starts chaining multiple llm invocations observability becomes an issue. Debugging poor app behaviour becomes laborous, especially with large prompts. Dedicated tooling for genai app observability solves this.
TODO: add observability tooling examples
#### EVALs 🤝 User feedback
Prompt engineering following your gut feel will hit its limits even with best observability tooling available. LLM models will inevitably drift, introducing new system instructions to address new failure modes can lead to degraded performance on existing failure modes, or you might simply want to start using a different model provider all together. Your genai application will start having degraded performance in unexpected ways.

Drawing from traditional swe experience the solution is obvious: a testing framework. This is where EVALs[1] come in. EVALs are testing your LLM models against a fixed set of labeled input prompts. Labeled in the sence that we know what output we're expecting for each one of these input prompts.

The key insight here comes after yet again a comparison to the world of traditional engineering. A start-up operating on this step of the ladder will be treating their EVALs like the source code: they're hard to acquire [2]. After all you will need to have users to bump into all the failure modes of the llm app hiding in remote nooks and crannies of the problem space. The prompts for a start-up operating on this step of the ladder will rather be more akin to compiled code: replacable and maybe even shareable.
#### Prompt Reliability Engineering

### Appendix

[1] Here we're referring to EVALs in the context of building applications on top of models that have been trained or fine-tuned by others. We ofcourse also need evaluation frameworks when training and fine-tuning models (optimizing for perplexity, loss functions etc.) We're not talking about these types of evaluations here. In other words we don't have access to all the individual values of the final llm layer, we're only working with wathever tokens the API sends us.

[2] Synthetic EVALs generation is a counter argument to this and will surely be a space that an industrious founder will want to explore.