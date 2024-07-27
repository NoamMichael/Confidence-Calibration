## Confidence Calibration of Large Language Models
Noam Michael<br>
Advised by Dr. Jacob Bien<BR>
USC Marshall School of Business, Data Science and Operations

Contact: <br>
nm_573@usc.edu<br>
noammichael1163@gmail.com

## Motivation

As Large Language Models (LLMs) have gotten more popular, their tendency to create new facts has become a growing issue. This tendency for models to "hallucinate" ideas makes their output less reliable which, in turn, makes users less likely to trust the model and continue to use them in the future. One way to combat this is by having models output a "confidence score" which tells the user how confident the model is of its answer. The hope is that users know when to trust a model's output and when to seek other sources. 
Throughout this project, we want to investigate if the LLMs can self-assess their understanding and accurately output a confidence score. 

### Examples of Hallucination:

* Lawyers submitted bogus case law created by ChatGPT. A judge fined them $5,000:<BR>
  https://apnews.com/article/artificial-intelligence-chatgpt-fake-case-lawyers-d6ae9fa79d0542db9e1455397aef381c

* ChatGPT makes nonsense diagnosis, cites fake papers:<BR>
  https://www.nature.com/articles/s41537-023-00379-4

* Google’s AI Recommended Adding Glue To Pizza:<BR>
  https://www.forbes.com/sites/jackkelly/2024/05/31/google-ai-glue-to-pizza-viral-blunders/

  ## Methodology

#### Model and Dataset:
For the sake of reproducibility, we used an open-source dataset as well as strict controls over contributing factors. Throughout this project, we ran in the same environment and eliminated randomness caused by variables like "temperature" and had the model sample using "Greedy Decoding" where it chose the token with the highest logit value

We used Llama 3-8B, an open-source LLM published by Meta. We also used BoolQ, an open source question answering dataset published by the University of Washington and Google AI.

##### Links:

###### Llama 3 Download: 
https://huggingface.co/meta-llama/Meta-Llama-3-8B

###### Llama 3 Info: 
https://ai.meta.com/blog/meta-llama-3/

###### BoolQ Dataset: 
https://github.com/google-research-datasets/boolean-questions?tab=readme-ov-file

###### Boolq Paper: 
https://arxiv.org/pdf/1905.10044

#### Llama 3:

Llama 3 is a Large Language Model trained to simulate a conversation between an Assistant and a User. We can provide Llama with a system prompt to define its goals and specify how we want its output to look. Following the system prompt, we can provide it with a passage and corresponding question from our BoolQ dataset. 

*Example System Prompt: (From Meta)*

><|begin_of_text|><|start_header_id|>system<|end_header_id|>
>
>You are a helpful AI assistant for travel tips and recommendations<|eot_id|><|start_header_id|>user<|end_header_id|>
>
>What can you help me with?<|eot_id|><|start_header_id|>assistant<|end_header_id|>

##### Note: The odd formatting is how we communicate directions to the model. See explanation below:

>**<|begin_of_text|>:** Specifies the start of the prompt <br>
>
>**<|start_header_id|>system<|end_header_id|>:** Specifies the role for the following message, i.e. “system”<br>
>
>**You are a helpful AI assistant for travel tips and recommendations:** The system prompt<br>
>
>**<|eot_id|>:** Specifies the end of the input message<br>
>
>**<|start_header_id|>user<|end_header_id|>:** Specifies the role for the following message i.e. “user”<br>
>
>**What can you help me with?:** The user message<br>
>
>**<|start_header_id|>assistant<|end_header_id|>:** Ends with the assistant header, to prompt the model to start generation.<br>

More documentation on this can be found here: https://llama.meta.com/docs/model-cards-and-prompt-formats/meta-llama-3/

#### BoolQ Dataset: 

The BoolQ dataset is designed to train and test large language models. It is comprised of two JSONL files with several thousand examples.<br> The files are:

**train.jsonl**: 9427 labeled training examples<br>
**dev.jsonl**: 3270 labeled development examples<br>

Both files are formatted similarly with a question, a title, an answer, and a corresponding passage. For example, Question #8 in the dev.jsonl file:

| Question | Title | Answer | Passage |
| -------- | -------- | -------- | -------- |
| Can an odd number be divided by an even number? | Parity (mathematics) | True | In mathematics, parity is the property of an integer's inclusion in one of two categories: even or odd. An integer is even if it is evenly divisible by two and odd if it is not even. For example, 6 is even because there is no remainder when dividing it by 2. By contrast, 3, 5, 7, 21 leave a remainder of 1 when divided by 2. Examples of even numbers include −4, 0, 82 and 178. In particular, zero is an even number. Some examples of odd numbers are −5, 3, 29, and 73.

### Definitions

#### Confidence Score
Throughout this project, we attempted both probabilistic confidence scores as well as stated confidence scores. We based both of these methods on prior conventions.

##### Probabilistic Confidence Score

We based the probabilistic confidence score on the same logic as the Probability of Precipitation (PoP) used by the National Weather Service. This is a probability where a given score indicates the likelihood that something will happen. In our case, our model should be correct 80% of the time it states it is 80% confident if it is perfectly calibrated.

An example output would look like:<BR>
**Assistant: Yes, 90%**

More info on PoP:<br>
https://www.weather.gov/media/pah/WeatherEducation/pop.pdf

##### Stated Confidence Score
Later in our research, we wanted to see if a stated confidence score would lead to better results. The motivation behind this was that the model may naturally have a better understanding of a stated word over a numerical score. We borrowed the idea of Words of Estimative Probabilities or WEPs from the intelligence community.

| Very Uncertain | Somewhat Uncertain | Moderately Certain | Fairly Certain | Very Certain |
|-|-|-|-|-|
| 53% | 65% | 75% | 87.5% | 96.5% |

For more documentation on the CIA's Words of Estimative Probabilities, see:
https://www.cia.gov/resources/csi/static/Words-of-Estimative-Probability.pdf

