## Road To Prompt Reliability Engineering
### Introduction
This article introduces the concept of Prompt Reliability Engineering as the final step on the prompt-ops maturity ladder. We will walk up this ladder and talk through the concepts and best practices on each of the steps. If you just deployed your first genai app and you'''re starting to get overwhelmed scrolling through the application logs or if prompt engineering  seems like taking one step forward and two steps back then this article is for you.

### Prompt-ops maturity levels
#### 1.Yoloing prompts
This is where every project starts and many end. If you update a piece of text in VScode, hit save ,and then go on to refresh a web-page to try the changes in a chat app, then you'''re likely in this stage. You'''re hopefully using github or some type of source code versioning to save the changes to your prompts. Bonus points if you'''ve set-up some type of ci/cd workflow that also deploys changes commited to your production app.
#### 2.Logging & tracing
Once the llm app becomes more sophisticated and the developer starts chaining multiple llm invocations observability becomes an issue. Debugging poor app behaviour becomes laborous, especially with large prompts. Dedicated tooling for genai app observability solves this. Tools that offer visualizations of chained invocations and traces can be particularly helpful for debugging complex agentic behaviour.
#### 3.EVALs 🤝 User feedback
Prompt engineering following your gut feel will hit its limits even with best observability tooling available. LLM models will inevitably drift, introducing new system instructions to address new failure modes can lead to degraded performance on existing failure modes, or you might simply want to start using a different model provider all together. Your genai application will start having degraded performance in unexpected ways.

Drawing from traditional swe experience the solution is obvious: a testing framework. This is where EVALs[1] come in. EVALs are testing your LLM models against a fixed set of labeled input prompts. Labeled in the sence that we know what output we'''re expecting for each one of these input prompts.

The key insight here comes after yet again a comparison to the world of traditional engineering. A start-up operating on this step of the ladder will be treating their EVALs like the source code: they'''re hard to acquire [2]. After all you will need to have users to bump into all the failure modes of the llm app hiding in remote nooks and crannies of the problem space. The prompts for a start-up operating on this step of the ladder will rather be more akin to compiled code: replacable and maybe even shareable.

GenAI evaluation is a rich field, with different modes, criteria, and metrics.
*   **Modes:** Can be Notebook-based for online services or Pipeline-based for offline services.
*   **Criteria:** Can be Pointwise (evaluating a single output) or Pairwise (comparing two outputs).
*   **Metrics:** Can be Model-based (e.g., summarization quality, coherence) or Computation-based (e.g., BLEU, ROUGE).
*   **Agents:** Evaluation can be on the final response or the trajectory of the agent (e.g., tool use, match order).

#### 4.Prompt Reliability Engineering
Prompt Reliability Engineering (PRE) is the practice of applying Site Reliability Engineering (SRE) principles to AI-powered applications. It provides a framework for measuring and maintaining the quality and reliability of your LLM applications. SRE has been a revolutionary force in how we think about building and maintaining traditional software systems. It provides a data-driven approach to balancing reliability with the need to innovate and release new features. The core idea is to define what reliability means for your application, measure it, and then use that data to make informed decisions. Let'''s explore how we can apply these battle-hardened SRE principles to the new frontier of GenAI applications.

The core concepts of SRE can be mapped to PRE:

*   **Critical User Journey (CUJ):** A Critical User Journey is a sequence of steps that a user takes to accomplish a meaningful goal with your application. Identifying CUJs is the first step in applying SRE/PRE because it forces you to think about your application from the user'''s perspective.

    *   **Traditional SWE Example:** For an e-commerce website, a CUJ would be "searching for a product, adding it to the cart, and successfully checking out." This is a critical flow that directly impacts the business.

    *   **LLM/Agentic App Example:** For an airline booking agent, a CUJ could be "booking a flight". This might involve multiple steps like the agent understanding the user'''s request, using a `flight_search` tool, presenting options to the user, and finally using a `flight_booking` tool to complete the transaction. Another CUJ could be "canceling a flight", which would involve a different set of tools and interactions.

*   **Service Level Indicator (SLI):** A Service Level Indicator is a quantitative measure of some aspect of the level of service that is being provided. SLIs are the metrics you use to determine if you are meeting your reliability goals.

    *   **Traditional SWE Example:** For the e-commerce checkout CUJ, an SLI could be the *availability* of the checkout service, measured as the percentage of successful checkout requests. Another SLI could be the *latency* of the checkout service, measured as the time it takes to complete a checkout.

    *   **LLM/Agentic App Example:** For our airline booking agent'''s "booking a flight" CUJ, we can define several SLIs:
        *   **Tool/Agent Selection Accuracy:** What percentage of the time does the routing agent correctly select the `Booking Agent` for a booking request? This is a measure of the routing agent'''s effectiveness.
        *   **Factual Correctness:** When the agent presents flight options, what percentage of the time is the information (flight number, departure time, price) correct? This is a measure of the quality of the information provided by the agent. This can be measured by comparing the agent'''s response to a ground truth source (e.g., the airline'''s database).
        *   **Task Completion Rate:** What percentage of users who start the "book a flight" journey are able to successfully complete it? This is a high-level SLI that measures the overall success of the CUJ.

*   **Service Level Objective (SLO):** A Service Level Objective is a target value or range of values for a service level that is measured by an SLI. SLOs are your reliability goals. They are a public declaration of the level of service you are committing to provide.

    *   **Traditional SWE Example:** For the e-commerce checkout service, an SLO for availability might be "99.9% of checkout requests will be successful over a 28-day period." An SLO for latency might be "99% of checkout requests will complete in under 1 second."

    *   **LLM/Agentic App Example:** For our airline booking agent, we can set the following SLOs:
        *   **Tool/Agent Selection Accuracy SLO:** "The routing agent will select the correct agent for the user'''s request 95% of the time over a 28-day period."
        *   **Factual Correctness SLO:** "99% of the flight information presented to the user will be factually correct over a 28-day period."
        *   **Task Completion Rate SLO:** "90% of users who start the '''book a flight''' journey will successfully complete it over a 28-day period."

*   **Error Budget:** The Error Budget is the amount of acceptable unreliability. An error budget is `1 - SLO`. It provides a data-driven way to balance reliability work with feature development. If you have not exhausted your error budget, you are free to innovate and take risks. If you have exhausted your error budget, you must focus on reliability.

    *   **Traditional SWE Example:** If the SLO for checkout availability is 99.9%, the error budget is 0.1%. This means that out of 1000 checkout requests, only 1 can fail. If the number of failures exceeds this, the team must stop all new feature development and focus on fixing the reliability issues.

    *   **LLM/Agentic App Example:** For our airline booking agent'''s factual correctness SLO of 99%, the error budget is 1%. This means that for every 100 flight information responses, only 1 can be incorrect.
        *   **When budget is available (Prioritize Velocity):** The team can focus on adding new features. For an LLM app, this could mean:
            *   Integrating new tools/APIs (e.g., a hotel booking API).
            *   Adding new RAG sources (e.g., a travel guide).
            *   Experimenting with new models (e.g., a more powerful but potentially less reliable model).
        *   **When budget is exhausted (Prioritize Reliability):** The team must focus on improving the reliability of the agent. For an LLM app, this could mean:
            *   **Prompt engineering refinements:** Improving the prompts to make the agent more accurate.
            *   **RAG pipeline tuning:** Improving the quality of the information retrieved by the RAG system.
            *   **Fine-tuning and specialization:** Fine-tuning the model on a dataset of correct flight information.
            *   **Performance optimization:** Reducing the latency of the agent'''s responses.

### Tooling
Several tools can help you implement PRE. The presentation mentions the Vertex AI suite of tools, including the GenAI Evaluation Service, Agent Evaluation Service, and Prompt Optimizer. BigQuery can be used as a backend for storing and analyzing evaluation data. The Agent Development Kit (ADK) is also mentioned as a relevant tool.

### Appendix

[1] Here we'''re referring to EVALs in the context of building applications on top of models that have been trained or fine-tuned by others. We ofcourse also need evaluation frameworks when training and fine-tuning models (optimizing for perplexity, loss functions etc.) We'''re not talking about these types of evaluations here. In other words we don'''t have access to all the individual values of the final llm layer, we'''re only working with wathever tokens the API sends us.

[2] Synthetic EVALs generation is a counter argument to this and will surely be a space that an industrious founder will want to explore.
