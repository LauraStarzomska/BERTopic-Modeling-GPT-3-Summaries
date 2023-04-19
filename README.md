# Twitter Project

* General info
* Technologies
* Files
* Steps
* Setup

## General info

The project aims to write a summary of tweets about #COP27 published between 30.10-7.11.22.

## Technologies

This project is created with:
* Tweets scraping using *sns-scrape* and/or *tweepy* modules
* BERTopic model
* GPT-3: *text-davinci-003 model*


## Files
* scraping - file with two scraping codes (tweepy and sns-scrape) and result .csv and .xlsx files with tweets
* topic_modeling - this folder includes python BERTopic modelling file to divide tweets into categories (different topics) and later using GPT-3 summarize it by them
* summary - it includes GPT-3 python file that summarises tweets into concise text

## Steps 

1. Scraping data
2. Cleaning data
3. Topic modeling (dividing tweets into categories to summarise them by topic)
4. Writing a GPT-3 summary:
- Dividing tweets into chunks (maximum tokens for model is 4096, to overcome this limitation we can build chunks)
5. Summarise tweets by topics and by chunks within topics, then summarise everything together.

## 1. Scraping data
In the folder *scraping* there are two python files: *scraper.ipynb* and *tweepy.ipynb*. 
* *scraper.ipynb* uses snscrape library. For snscrape we do not need developer account and credentials and we are able to scrape limitless tweets. However, compared to tweepy it can be quite limited with details about users or tweets. For this project however snscrape was sufficient. More about library here: https://github.com/JustAnotherArchivist/snscrape
* *tweepy.ipynb* uses tweepy library with V2 API. To use this package we need developer account which can be set up here https://developer.twitter.com/en/support/twitter-api/developer-account with OAuth 2.0 we are able to scrape **500k Tweets per Month**

## 2. Cleaning data
Cleaning is done in the same files as scraping. We had to delete from tweets *url*, *nicknames*, *enter space*, transform date format and keep only tweets longer than 15 characters. Emojis will be only deleted in the stage of topic modeling (tokenization), but then for summarisation we will need them back. 

## 3. Topic Modeling
We have so many different tweets that it would be hard to summarise them in one concise text. In order to have better outputs we can divide our tweets by topics and then summarise tweets by them. We tried different Topic Modeling Models(can be found here https://huggingface.co/modelss) but the best performance was produced by *BERTopic*

> BERTopic is a topic modeling technique that leverages 🤗 transformers and c-TF-IDF to create dense clusters allowing for easily interpretable topics whilst keeping important words in the topic descriptions.

More about BERTopic here https://arxiv.org/abs/2203.05794 (worth reading article)

* In the file *topic_modeling* we can find python script *BERTopic_modeling.ipynb* with explained code that divides our tweets into topics and then merge topic-categorization with cleaned topics (without links, nicknames, longer than 15 char. etc.) 
* We ended up with 5 uncorelated topics

## 4. GPT-3 Summary

There are a lot of summary models available in the programming world (can be found here: https://huggingface.co/models?pipeline_tag=summarization&sort=downloads) but the best one in terms of performance, complexity, and human-alike outputs are provided by GPT-3 model. 

> GPT-3, or the third generation Generative Pre-trained Transformer, is a neural network machine learning model trained using internet data to generate any type of text. Developed by OpenAI, it requires a small amount of input text to generate large volumes of relevant and sophisticated machine-generated text.

More about GPT-3 model: https://neuroflash.com/blog/gpt-3/

In the file *summary* we can find python script *gpt3_summarization.py*, *file.txt*., *prompt.txt*, *gpt.logs* and *output_* folders. In the main gpt3_summarization.py python file there is a code to divide text from file.txt file into chunks and summarize them separately. In the file.txt we insert tweets from particular topic which we got in the topic_modeling part. So, we insert all tweets from one particular topic into file.txt. The code divides text from file.txt into smaller chunks and then replaces **SUMMARY** part in the prompt.txt file with chunks. Every GPT-3 model needs prompt (> The prompt is literally just a request written in normal, natural language, asking it want it to do). Our prompt says "Write a detailed summary of the following tweets: **SUMMARY** DETAILED SUMMARY:" For such sensitive AI model as GPT-3 every word in the prompt matters so if we deleted 'detailed' from out request in the prompt the output would be totally different. **SUMMARY** part is replaced with tweets chunks from file.txt and it summarises it separately. By doing so, we can overcome limitation problem (4096 tokens). When we run *gpt3_summarization.py* from terminal the output is saved in *output.txt* file. Also detailed summarization chunk by chunk can be found in *gpt_logs* file. 
