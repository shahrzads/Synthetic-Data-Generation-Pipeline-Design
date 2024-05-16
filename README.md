# Synthetic-Data-Generation-Pipeline-Design
## Overview
This document outlines the design and implementation of a synthetic data generation pipeline. The pipeline utilizes **OpenAI**, **Hugging Face**, and **pandas** for generating synthetic datasets and automatic responses.


## Pipeline Workflow

**Synthetic Dataset Generation Procedure**:

1. **Load Initial Dataset** (140 Queries)
   - Retrieve the dataset from Hugging Face.
   - Extract questions from the dataset.
   - **Note:** The input dataset consists of 140 rows and 2 columns:
     
         1. ID: A unique identifier for each question.
         2. Question (Query): The text of the question to be processed.
     
2. **Preprocess Data** 
   - Convert text to lowercase.
   - Remove punctuation.
   - **Note**: In text generation tasks such as synthetic data and response generation, lemmatization might not be beneficial. For this task, lemmatization was not applied to preserve the diversity of word forms, ensuring natural text generation and fluency.

3. **Construct Prompt**
   - Create a prompt by combining the input question with a modification instruction based on question structure and context.
   - **Note:** The modification instruction helps in generating diverse and contextually rich synthetic questions.
   
4. **Generate Synthetic Questions**
   - Use OpenAI's API to generate synthetic questions based on the constructed prompt created in the previous step.

5. **Save Dataset**
   - Save the generated synthetic dataset to `questions.csv`.

**Automatic Response Generation**

The goal is to generate responses for supervised fine-tuning of LLMs via Direct Policy Optimization (DPO). To pursue this goal, the final output should include 3 columns that contain questions, chosen responses, and rejected responses. In other words, for each input question, we need both correct (chosen) and incorrect (rejected) responses.

1. **Generate Responses**
   - Use the `generate_response` function to generate correct and incorrect responses for the synthetic questions generated in step 4 of the Synthetic Dataset Generation Procedure.
    - To generate responses, boolean `chosen` parameter was passed to `generate_response` function. This parameter determines if the chosen or rejected responses should be passed as the function output.
   - **Note**: The function utilizes OpenAI's API to generate responses.
  
2. **Save Responses**
   - Save questions along with their chosen and rejected responses to `questions_and_responses.csv`.


## Quality Evaluation of the Generated Questions and Responses

Evaluating the quality of synthetically generated questions and their corresponding responses (chosen and rejected), especially in a specialized domain like healthcare, requires careful consideration of both linguistic quality and domain-specific accuracy. Here are some metrics and criteria that can help to assess the effectiveness of the generated dataset:

1. **Linguistic Quality**
   - **Fluency**: Assess grammatical correctness and logical flow.
   - **Coherence**: Ensure the responses logically align with the questions.
   - The following statistical and model-based metrics can be used for fluency and coherence analysis:
     - **Word Error Rate (WER)**
     - **Perplexity**
     - **BLEU Score**
     - **BERTscore**

2. **Domain-Specific Accuracy**
   - **Factual Accuracy**: Ensure the responses contain medically accurate and current information.
   - **Consistency**: Evaluate whether the responses are consistent with the information generally accepted within the healthcare domain, avoiding contradictory or internally inconsistent content.
   - **Relevance**: Confirm responses address the questions directly and relevantly.
   - **Misinformation Rate**: Ensure rejected responses are clearly incorrect.

**Tools and Methods for Evaluation**

- **Automated Quality Checks**: Use tools like specialized LLMs trained on medical data to check factuality and Grammarly or LanguageTool to check grammatical correctness.
- **Manual Review**: Employ domain experts like healthcare professionals to manually assess the quality and accuracy of a sample.
- **User Satisfaction Testing**: Collect qualitative and quantitative feedback from end-users based on the questions that have been asked and the chosen and rejected responses.

These evaluation strategies will help ensure that the generated synthetic dataset with the questions and the corresponding correct and incorrect responses is not only linguistically correct but also safe to use in practical healthcare applications.

## Domain Adaptation (Non-Healthcare Domains)

Adapting the current pipeline, which is designed for generating healthcare-related questions and responses, to new non-healthcare domains involves several challenges and opportunities. The key insights into the potential challenges and solutions for domain adaptation are as follows:

### Challenges

1. **Domain-Specific Knowledge**
   - **Issue**: Each domain requires an understanding of its unique language and context.
   - **Solution**: Tailor the language model by training, fine-tuning, prompting, or employing RAG using domain-specific datasets to improve the synthetic data generation

2. **Modification List Alignment**
   - **Issue**: The "modifications list" for generating synthetic questions in healthcare may not suit other domains.
   - **Solution**: Develop a dynamic modifications list that is domain-aware. This can be achieved by:
      - **Automating the Modifications List Creation**: Use domain-specific data to automatically generate relevant modifications. This can involve natural language processing techniques to identify common linguistic structures or queries in the new domain.
      - **Expert Validation**: Regularly consult with domain experts to refine and validate the modifications list, ensuring it accurately reflects the needs of the new domain.
      - **Continuous Learning**: Use feedback from generated question and response performance to continuously improve the modifications list.

3. **Data Quality and Reliability**
   - **Issue**: Domains like law, finance, or even technical fields such as engineering require high accuracy to avoid misinformation.
   - **Solution**: Implement enhanced validation processes, possibly automated combined with expert reviews.

4. **Regulatory, Ethical, and Bias Concerns**
   - **Issue**: Different domains are subject to specific regulations, particularly concerning data privacy.
   - **Solution**: Ensure compliance by staying updated with regulatory frameworks and implementing ethical guidelines in data handling processes.

Adaptation strategies to handle changes in the domain:

1. **Modular Design**: Create a modular and flexible architecture that allows for easy swapping of components specific to each domain.
2. **Domain-Specific Metrics**: Develop evaluation metrics that reflect the specific requirements of the new domain.
3. **Collaborate with Experts**: Work with domain experts during development and evaluation to ensure relevance and accuracy.

## Lessons Learned from Experiments and Next Steps
The following lessons were noticed while executing multiple experiments.
- Using frameworks [Cosmopedia](https://github.com/huggingface/cosmopedia/tree/main/generation) and [LLM-Swarm](https://github.com/huggingface/llm-swarm/tree/loubna/examples/textbooks) could potentially assist in having a more scalable model, as the Cosmopedia dataset consists of 25 billion tokens. Although this approach was explored, it was not fully implemented due to limitations with `slurm` (`sbatch` was not functional on Mac OS).
 
- After adjusting the initial HuggingFace dataset and the LLM model to use, the above-mentioned framework needs `slurm` to run successfully. Providing such services can increase the scalability, efficiency, and ease of use for synthetic data generation, while `slurm` requires specific hardware systems, and not all machines have it.

- Following the adjustment of the initial Hugging Face dataset (healthsearchqa) and the selection of the appropriate LLM model (Mistral, jais), it was found that the aforementioned frameworks necessitate the use of `slurm` for effective operation. This could significantly enhance scalability, efficiency, and usability in synthetic data generation.

- Furthermore, there was an effort to employ Mistral; however, it could not be utilized due to its associated costs.

- 
```
