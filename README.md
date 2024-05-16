# Synthetic-Data-Generation-Pipeline-Design

## Pipeline Workflow
Frameworks used for synthetic dataset generation and response generation: **openai, huggingface, pandas**

**Synthetic Dataset Generation Procedure**:

1. Load the initial dataset from huggingfce and extract its questions
2. Preprocess the data including converting to lower case and removing punctuations (refer to the codebase for more details)
   - In text generation tasks like synthetic data generation and response generation, lemmatization might not be beneficial, because preserving the diversity of word forms is important for generating natural and fluent text.
3. Construct a prompt to modify a given question.
   - This function is responsible for creating a prompt. The prompt is a combination of the input question and a modification instruction of the question based on its structure and information.
4. Use OpenAI's api to generate synthetic questions based on the generated prompt in the previous step.
5. Save the generated synthetic dataset (`questions.csv`).

**Automatic Response Generation**

Since the goal is to generate responses for supervised fine-tuning of LLMs via Direct Policy Optimization (DPO), the final output should contain question, chosen response, and rejected response columns. So, for each question, we need both correct and incorrect responses. The `generate_response` function is responsible for generating the responses (either correct or incorrect), based on the passed boolean parameter `chosen`. The response is generated based on the prompt that asks OpenAI api to generate a correct/incorrect response for the given question.

Finally, all the questions and their corresponding chosen/rejected responses are saved in a file (`questions_and_responses.csv`)



## Quality Evaluation of the Generated Questions and Responses
Evaluating the quality of synthetically generated questions and their corresponding responses (chosen and rejected), especially in a specialized domain like healthcare, requires careful consideration of both linguistic quality and domain-specific accuracy. Here are some metrics and criteria that can help to assess the effectiveness of the generated dataset:
1. **Linguistic Quality**
   - **Fluency**: Access grammatical correctness and logical flow
   - **Coherence**: Ensure the responses logically align with the questions
  
  
2. **Domain-Specific Accuracy**
   - **Factual Accuracy**: Ensure the responses contain medically accurate and current information
   - **Consistency**: Evaluate whether the responses are consistent with the information generally accepted within the healthcare domain, avoiding contradictory or internally inconsistent content
   - **Relevance**: Confirm responses address the questions directly and relevantly
   - **Misinformation rate**: Ensure rejected responses are clearly incorrect

  
**Tools and Methods for Evaluation**
- **Automated Quality Checks**: Use tools like specialized LLMs trained on medical data for factual checks and Grammarly for grammatical correctness
- **Manual Review**: Engage domain experts like healthcare professionals to manually assess the quality and accuracy of a sample
- **User Satisfaction Testing**: Collect qualitative and quantitative feedback from end-users based on the questions that have been asked and the chosen and rejected responses

These evaluation strategies will help ensure that the generated synthetic dataset with the questions and the corresponding correct and incorrect responses is not only linguistically correct but also safe-to-use in practical healthcare applications.

## Domain Adaptation (non-healthcare domains)
Adapting the current pipeline, which is designed for generating healthcare-related questions and responses, to new non-healthcare domains, involves several challenges and opportunities. The key insights into the potential challenges and solutions for domain adaptation are as follows:
### Challenges
1. **Domain-Specific Knowledge**
   - **Issue**: Each domain requires understanding of its unique language and context
   - **Solution**: Tailor the language model by training or fine-tuning on domain-specific datasets to have a small version of the dataset (to do the synthetic data generation on)
2. **Modification List Alignment**
   - **Issue**: The modifications list for generating synthetic questions in healthcare may not suit other domains
   - **Solution**: Develop a dynamic modifications list that is domain-aware. This can be achieved by:
      - **Automate the Modifications List Creation**: Use domain-specific data to automatically generate relevant modifications. This can involve natural language processing techniques to identify common linguistic structures or queries in the new domain.
      - **Expert Validation**: Regularly consult with domain experts to refine and validate the modifications list, ensuring it accurately reflects the needs of the new domain.
      - **Continuous Learning**: Use feedback from generated question and response performance to continuously improve the modifications list
4. **Data Quality and Reliability**
   - **Issue**: High accuracy requirements for some domains like law, finance, or even technical fields such as engineering due to the consequences of misinformation
   - **Solution**: Implement enhanced validation processes, possibly automated combined with expert reviews
7. **Regulatory and Ethical Concerns**
   - **Issue**: Different domains are subject to specific regulations, particularly concerning data privacy
   - **Solution**: Ensure compliance by staying updated with regulatory frameworks and implementing ethical guidelines in data dandling processes
  
Adaptation strategies to handle changes in the domain:
1. **Modular Design**: Create a modular and flexible architecture that allows for easy swapping of components specific to each domain
2. **Domain-Specific Metrics**: Develop evaluation metrics that reflect the specific requirements of the new domain
3. **Collaborate with Experts**: Work with domain experts during development and evaluation to ensure relevance and accuracy


## Lessons Learned and Next Steps
- Using frameworks [Cosmopedia](https://github.com/huggingface/cosmopedia/tree/main/generation) and [llm-swarm](https://github.com/huggingface/llm-swarm/tree/loubna/examples/textbooks) can assist in having a more scalable model, as Cosmopedia dataset consists of 25 billion tokens.
- After adjusting the initial huggingface dataset and the LLM model to use, the above-mentioned framework needs `slurm` to run successfully. Providing such services can increase the scalability, efficiency, and ease of use for synthetic data generation, while `slurm` requires specific hardware systems, and not all machines have it.
