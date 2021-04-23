Full Report Available at https://docs.google.com/document/d/e/2PACX-1vTpD3C59LIpqFdQj4DkC9Bmoy5sMk-ceBw2n0T9rYfjSgVj2x-uM9EVtfLBH6Xyj2RNYTACgfRMazUt/pub

Summary of the Project

This project aims to inherit a model fully learned and extend it to consume a completely new set of words. The process involves the following high level steps

Train a standard chatbot model on text X. For this example, we have used data from movie dialogues
Construct a dataset Y completely different from X. For this example, we have developed Y using stackoverflow discussions
Augment the chatbot machine saved from step 1 using dataset Y
Test the results of new model by validating if the model is capable of executing words from both Text X and Text Y

Motivation : Real world applications of this approach

Business application of this project is primarily oriented towards closed chat systems based on current understanding of the chatbot model. As an example consider chatting platforms used in specialized stock trading systems, where new words are added on a regular basis such as new ticker symbols. 

It is difficult to retrain models in its entirety from scratch for additional words. Therefore, a more cost effective model would be to figure out an approach of augmenting the vocabulary and enhance the models to accommodate the new vocabulary. 

References

This project is built using the base code base available at : 
https://pytorch.org/tutorials/beginner/chatbot_tutorial.html?highlight=chatbot 

Stackoverflow scraping logic was utilized from the following location : 
https://melaniesoek0120.medium.com/stack-overflow-web-scraping-with-python-2a1931ed2bd5 

Introduction to Seq to Seq models
https://www.linkedin.com/pulse/explanation-attention-based-encoder-decoder-deep-keshav-bhandari/ 

Background on Chatbot design

Chatbots are engineered to identify the inherent relationship between word sequences based on the text they are trained on. Given below are the fundamental elements of a chatbot machine

Vocabulary Object : Contains a list of all words, the position of each word in this list offers an important correlation to other objects that are used in defining the Chatbot. Each word would get a numerical index using this object.

Embedding Object : Contains a matrix encoded and trained from word indices (indices generated from Vocabulary object). Each word is represented by a vector in the matrix. If the vector is close to another, then those words are semantically close.

Encoder Object : network that accepts each input and generates an intermediate vector representation of each input sequence. Encode is trained along with / alongside the decoder.

Decoder Object : accepts the output of encoder and predicts words based on the original input sequence. The decoder starts predicting the next word based on the intermediate vector produced by the encoder.

Project Steps

Step 1 : Train the chatbot for original Input Data

The train process was executed as originally coded in the chatbot architecture for a total of 4000 training iterations. The resulting ML Model is saved into the disk with the following objects

'iteration': iteration,
'en': encoder.state_dict(),
'de': decoder.state_dict(),
'en_opt': encoder_optimizer.state_dict(),
'de_opt': decoder_optimizer.state_dict(),
'loss': loss,
'voc_dict': voc.__dict__,
'embedding': embedding.state_dict()

Step 2 : Generate StackOverflow training data

The logic implemented to generate this portion of data involves scraping the stack overflow lines from the following URL

https://stackoverflow.com/questions?tab=votes&page=?sort=votes&pagesize=50

It generates 50 items per URL invocation. The logic converts resulting items on the page response and scrapes the items into the format of the data set that was used in the chatbot program.

Step 2 : Load the encoder and decoder from saved objects
The new vocabulary object has increased in size given the new words we are looking to add. Therefore the original encoder and decoder objects will not match with the new vocabulary object. To address this problem, we create the encoder and decoder using the original (movie dialogue) vocabulary object. With this baseline we can now load the encoder and decoder from saved objects.



Step 3 : Create a merged vocabulary and embedding

Vocabulary : 
Read each line and its response from movie data set
Parse the lines to dissect the words and add to VOC object 
Read the stackoverflow data set 
Parse the lines from stackoverflow data set and add the words to main VOC object
Our code holds a total of 3 VOC objects to identify the vocab of each individual dataset and a combined one.

Embedding : 
Load the embedding from saved objects representing original movie data set
Generate a new embedding object based on the VOC of stackoverflow data set 

It is important to hold the relative position of words in the VOC vector and (corresponding) embedding vector as we recreate the objects. This correlation must be maintained to avoid disrupting the vectors learned as part of the training process into the embedding objects. 

Step 4 : Reset embedding object of encoder and decoder 

We now have the new merged embedding object that combines the full vocabulary of movie lines and stack overflow text. So we now set the inherent embedding of the  encoder and decoder to this new embedding object.

Step 5 : Train a new model using updated objects

A second round of training is executed using the combined vocabulary and merged Embedding object. This second training executes a significant decrease in the training time give that it is parsing a much smaller data set in stack overflow
