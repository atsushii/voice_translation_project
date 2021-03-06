# Neural-Machine-Translation-Project


# Overview

Nowadays there are more than 6000 spoken languages in the world. We can't understand all of them, but we must involve with people who speak a different language because the society is growing globally.

Specifically, We must understand the detail of sentences properly when we chat with other people Also we know that learning different languages are needed a lot of time.

Deep learning is a powerful technique that can be used to solve this problem. I am using NLP for text translation.
In this project, try to translate English sentences to Japanese sentences.

# USE
You can try as API

**URL**

<http://3.134.69.63/translate

**Method**

<The request type>

```
GET
```
**Required**

{
    input: [English]
}

* must send request as a json

**Success Response**

{
    "English": "[English]",
    "Japanese": "[Japanese]",
    "status": 200
}

**Error Response**

{
    "response": "Bad request",
    "status": 400
}

# Installation
if you want to try on your local env, follow the below methods.

**Clone**

・Clone this repo to your local machine using: https://github.com/atsushii/Neural-Machine-Translation-Project.git

**Setup**

・Pull docker image from docker hub.
```
docker pull atsushiiii/workflow1-self-contained:latest
```

# Run

・Run docker image.
* 
```
docker run -d -p 5000:5000 [IMAGE]:[TAG]

curl -d '{"input":"[English]"}' -H "Content-Type: application/json" -X GET http://0.0.0.0:5000/translate
```

# Stop
```
docker kill [CONTAINER ID]
```


# Neural Machine Translation

I am using the seq2seq model which is usually usu for machine translation, speech recognition, and text summarization.
NMT can translate language to language.

NMT is using teacher forcing in the training process.


**Dataset** 
https://nlp.stanford.edu/projects/jesc/index.html


# Architecture

My architecture is  Encoder-Decoder with attention mechanism.
This model is created by several layers which are recurrent neural(RNN) network and word embedding layer both of them are used to by most MNT models. Usually, an RNN and word embedding are used for both the encoder and decoder.
There are different types of RNN, a Long Short-Term Memory(LSTM) and gated recurrent unit(GRU).
In this project, I used an LSTM.

![Untitled (13)](https://user-images.githubusercontent.com/25543738/74544911-cdd16a00-4efc-11ea-9090-b49ff86f2ed5.png)


  **Embedding**
  
  Word embedding is a type of word representation that is able to convert word to vector.
  After embedded words that have similar representations with words that have similar meanings.
  
  But why need an embedding?
  without embedding, there are several problems to handle text data
  
  ・ Similarity problem

  As I mentioned after embedded words, we will have similar representations with words that have similar meanings.
  But without embedding, we won't have a similar representation with each word.
  Because each word would be one-hot encoding which means each word is just different word even though each word has similar     meaning such as "Mother" and "Father"
  
  ・ Size problem 

  We have the number of n vocabulary, one-hot vector will have n dimensions.
  Let's say vocabulary size is 1000000 one-hot vector will have 1000000 dimensions,
  we need enough memory and storage otherwise we can not train the model.
  Another reason is the increasing number of vocabulary also increase feature size vectors.
  It means there are a lot of parameters to estimate, we need more data to estimate these parameters
  if we build a good model.
  
  Word embedding able to solve those problem!
  
  
  **LSTM**
  
  Before explain what is LSTM let me comparing basic neural network and RNN first.

  Let's say we use basic deep neural network there are 3 hidden layers, each hidden layer has biases and weights.
  (w1, b1), (w2, b2), (w3, b3)
  These layers are independent of each other also they don't memorize previous output.
  
  Recurrent Neural Network is a type of Neural Network.
  it has memory allowing information to persist.
  
  ![Untitled (3)](https://user-images.githubusercontent.com/25543738/74124555-84dd8680-4b87-11ea-8d43-0127181598d7.png)

  The loop is able to provide previous information to next.
  RNN has the same parameter for each input so it can reduce the complexity of the parameter.
  
  But RNN has long-term dependencies problem.
  if we have a long sentence, the gap is big between the target word and relevant information.
  RNN unable to learn to connect the information.
  
  Fortunately, LSTM is able to avoid this problem.
  
  LSTM is similar to RNN
  It is designed to avoid long-term dependencies problems.
  SO LSTM is able to persist long term information!
  
  As RNN has a chain of repeating module of neural network,
  this module has a simple structure.
  It is contain a single layer such as tanh
  
  ![Untitled (4)](https://user-images.githubusercontent.com/25543738/74288775-2678eb00-4ce2-11ea-95b5-21ce20a73821.png)
  
  LSTM has also the same chain structure but having a different repeating module instead of containing a single layer,
  there are able to have multiple layers.
  
  ![Untitled (9)](https://user-images.githubusercontent.com/25543738/74467470-f77f8800-4e4d-11ea-80ea-8192e55f2730.png)
  
  these are several gates, each gate will keep important data in sequence or throw away if it's not important.
  
  The cell state is able to carry a relative information to next time step or even more.
  Until cell state is gone, information is added or removed to the cell state.
  each gate are different neural network that will choose which information is important as I mentioned each gate are     
  different neural network so they will learn which information is relevant to keep or forget during training.
  
   **sigmoid**
   
   As you see the gate contains are sigmoid functions.
   sigmoid function of range of output is between 0 to 1.
   It is helpful to update or forget data the reason is any number will get 0 if multiplied by 0.
   on the other hand, any number will get the same number if multiplied by 1 that means "try keep"
   So neural network will learn which data is important to keep or not important to forget.
   
   also we have different gates inside a LSTM
   
   **Forget gate**
   
   This gate will decide what information will keep or throw away.
   The information from previous hidden state and input value are into sigmoid function.
   output value will be between 0 to 1, if closer to 0 is going to forget, however closer to 1
   it going to keep.
   
   ft = σ(Wf * [ht-1 + xt] + bf)

   
   **Input gate**
   
   The input gate receives previous hidden state and input value, these values into the sigmoid function.
   Input gate will update a value that will be between 0 or 1. 0 is not important, 1 is important.
   Also previous hidden state and input value into tahn function. this function of range of output is between -1 to 1, it can 
   help to regulate the network.
   after these section we multiple sigmoid output and tahn output.
   Then the result is 1 this result is going to add to the cell state as new information however old information will be gone.
   
   it = σ(Wi * [ht-1 + xt] + bf)

   C~ = tanh(Wc * [ht-1 + xt] + bc)
   
   **Call state**
   
   Cell state has pointwise multiplication and pointwise addition.
   After finishing forget gate, there is pointwise multiplication.
   Cell state gets value which is between 0 to 1 because this value through a sigmoid function.
   if the output value is closer to 0 dropping in the cell state.
   Then after input gate, there is a pointwise addition that is going to update the cell state as new relevant information.
      
   update cell state
   
   Ct = ft * Ct-1 + C~ * it
   
   **Output gate**
   
   Output gate is the last part of layer, it will decide what is the next hidden state.
   First we put current input and previous hidden state into a sigmoid function (return 0 to 1)
   Then cell state into the tanh function (return -1 to 1),
   after that we multiple by output of sigmoid and output of tanh to decide what information we carry to 
   next time step as a hidden state.
   
   ot = σ(Wo * [ht-1 + xt] + bo)
   ht = ot * tanh(Ct)
 
 **Encoder**
  
 Encoder is given source language as input.
 Input is split by word.
 
 ![Untitled (10)](https://user-images.githubusercontent.com/25543738/74473057-d02db880-4e57-11ea-9f94-2b26e65add6a.png)
 
 For instance, input contains 4 words it means t = 4.
 
 Each word is represented as a vector because already through the embedding layer which I already explained.
 Remember final state(h4, c4) contains input sequence "I have a cat"
 So encoder is just read each input word and store the final state.
 Basically LSTM will be generated entier sequence so we do need to keep a output from lstm
 because I already had a sequence as a final state, but in this project, I use it for attention mechanism.
 
 **Decoder**
 
 Decoder will generate an output sentence
 
 e.g.

 Decoder output: "私は猫を飼っている"

 Encoder input: "I have a cat".

 but the decoder needs to recognize when start a sequence and end a sequence.
 I added START_ at the beginning of the input sentence also added _END at the end of the output sequence
 
 
 ![Untitled (11)](https://user-images.githubusercontent.com/25543738/74488198-4c36f900-4e76-11ea-83da-b108e483bd97.png)

 The initial state is the same as the final state of encoder. decoder model is trained to generate a target word
 it is based on information from encoder.
 After input, a "start_" word into a decoder input, decoder starts to generate the next word also decoder
 end to generate the next word when decoder predicts a "_END" word.
 
 The decoder model is used teacher forcing technique. 
 So decoder output is not correspond with decoder input, it is ahead by 1 timestep from decoder input.
 Loss is calculated on the predicted output from each time step and errors are backpropagated to update 
 the parameter of the model.

 Without teacher forcing the hidden state will be updated by the wrong prediction, it means error will not decrease.
 
 
 **Attention Mechanism**

![Untitled (14)](https://user-images.githubusercontent.com/25543738/74579881-000db680-4f53-11ea-9814-c1f0f6a43ecb.png)

Attention is useful to predict a long sequence.
Because RNN can't memorize a long sequence, attention is able to learn what word needs to pay attention to.
Attention is also useful to get more accuracy.
 
I used Global Attention with dot-based scoring for attention mechanism

Global attention uses only encoder output and current decoder for the current timestep.

![Untitled (15)](https://user-images.githubusercontent.com/25543738/74581200-a496f500-4f61-11ea-825f-f9d95806bca3.png)

[Effective Approaches to Attention-based Neural Machine Translation](https://arxiv.org/pdf/1508.04025.pdf)











