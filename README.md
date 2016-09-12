# Sugar Tensor - A slim tensorflow wrapper that provides syntactic sugar for tensor variables
Sugar Tensor aimed to help practical deep learning researchers and developers not beginners. 
It adds some syntactic sugar functions to tensorflow to avoid tedious repeating jobs.
Sugar Tensor developed by current deep learning researchers under following principles:

## Principles
1. Don't fxxx up the tensorflow. We provide no wrapping classes and use tensor itself, 
therefore developers can program freely their code as before with tensorflow. 
1. Don't fxxx up the python style.  We think python program should look pretty and simple. 
Practical deep learning codes are very different from that of complex GUI programs. Really do we need inheritances 
and encapsulations in out deep learning code. Instead, we strive to simplicity and readability, so, 
we use only pure python functions as possible and avoid class style conventions.     

## Installation

1. Requirements
    1. tensorflow >= rc0.10 
    1. tqdm >= 4.8.4  ( for console progressive bar )

1. Installation
<pre><code>pip install sugartensor</code></pre>

## Quick start

###Imports

> `import sugartensor as tf   # no need of 'import tensorflow'`

## Features

### sugar functions

All tensors ( variables, ops, constants ) automatically have sugar functions which start with 'sg_' 
to avoid name space chaos. ^_^   

### Chainable object syntax
  
Inspired by prettytensor library we support chainable object syntax for all sugar functions. 
This could provides increased productivity and readability.  
Look following snippet.

`
logit = (tf.placeholder(tf.float32, shape=(BATCH_SIZE, DATA_SIZE))
         .sg_dense(dim=400, act='relu', bn=True)
         .sg_dense(dim=200, act='relu', bn=True)
         .sg_dense(dim=10))
`
 
### All returned objects are tensors.

In the above snippet, all return values of sugar functions are pure tensorflow's tensor variables ( or constants ). 
So, the following example is completely legal.

`
ph = tf.placeholder(tf.float32, shape=(BATCH_SIZE, DATA_SIZE)   # <-- this is tensor 
ph = ph.sg_dense(dim=400, act='relu', bn=True)   # <-- this is tensor
ph = ph * 100 + 10  # <-- this is ok.
ph = tf.reshape(ph, (-1, 20, 20, 1)).conv(dim=30)   # <-- all tensorflow's function can be applied and chained.
`

### practical DRY(Don't repeat yourself) for deep learning researchers.
  
We provide pre-defined but powerful training and report functions for practical developers.
A Following code is full mnist training module with saver, report and early stopping support.

`
# -*- coding: utf-8 -*-
import sugartensor as tf

# MNIST input tensor ( with QueueRunner )
data = tf.sg_data.Mnist()

# inputs
x = data.train.image
y = data.train.label

# create training graph
logit = (x.sg_flatten()
         .sg_dense(dim=400, act='relu', bn=True)
         .sg_dense(dim=200, act='relu', bn=True)
         .sg_dense(dim=10))

# cross entropy loss with logit ( for training set )
loss = logit.sg_ce(target=y)

# accuracy evaluation ( for validation set )
acc = (logit.sg_reuse(input=data.valid.image).sg_softmax()
       .sg_accuracy(target=data.valid.label, name='val'))

# train
tf.sg_train(loss=loss, eval_metric=[acc], ep_size=data.train.num_batch)
`

