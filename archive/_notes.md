Hi everyone welcome to my progress review presentation

Hi everyone, How's everyone doing today? Anyone here feeling like they won the lottery? Good well I'm about to tell the story of how I spent an entire year trying to fit neural networks with the worse luck possible, and how I was finally able to win the neural network lottery by veeery slim chances

Without further ado, let's jump right into it. Those of you who were here in last year's presentation would be very much familiar with what i'm about to say, but let's be honest here. Building a good model is hard yes, but explaining a good model is even harder. 

For example let's take this lil guy. A happy good looking model, we can't see what's happening inside it but we can prod the model and see how it responds to different scenarios through a set of methods called explainable AI methods. 

XAI methods are classified based on two parts, firstly whether they are model specific or model agnostic, and then whether the method is explaining the behavior of the model with respect to the entire dataset or how the model reacts to a specific observation.

Let's start things off by focusing on model agnostic local interpretability methods. Because most of the times we want personalized explanations instead of global explanations which might be harder to understand.

There are several XAI methods that fit into that category as seen here. Each method has its own method of answering a very specific aspect of the model. But there's something missing here.

Each of these methods are giving us a bunch of numbers and it doesn't make sense to look at these numbers as they are. So what if instead of looking at these numbers outside of the model and the data space we built geometric representations to draw these numbers on the model data space. 

Let's see how we can do that, starting off first with LIME. LIME tries to answer the question "How important were each of the features in giving this prediction" and the way LIME tries to answer it is by going "If only we could have super simple model that acts similar to the original black box model near a local neighborhood of the given observation, then we can try to see the importance of each feature by fitting that small model into a bunch of simulated data points within that neighborhood." In the actual implementation LIME uses a simple generalized linear model around the local neighborhood and returns the coefficients of the model as feature importances. Therefore to visualize values from LIME we can use regression lines like this.

SHAP values on the other hand, answer the question, "How much did every feature contribute to the final prediction". Simply put SHAP values consider each feature as a co-op game and the final prediction as the reward. Then several perturbations of the dataset is made by adding and removing features to see the influence each feature has on the prediction. Therefore to represent SHAP values we use force vectors along the axis of a feature to represent the. Therefore to represent SHAP values we use force vectors along the axis of a feature to represent them

Counterfactual values aim to answer the question "How much should the features change for the prediction to change". This question is then reframed as an optimization problem to find the closest feature with the least amount of changes that have the desired prediction from the model. Since Counterfactuals show the counter example from the dataset we can use connecting lines to visualize them in the data space.

Finally we have Anchors, and anchors tries to answer the question, "Where are observations located with the same predictions as the target observation of interest?" It's done by formulating the problem as a multiarm bandit problem to find boundaries that contain of the observations that have the same prediction as the given observation. Intuitively, we can draw anchors as bounding boxes around the given observation.

At the time I was working on proposing these representations, the active anchors package was quite hard to debug and was quite buggy. Therefore as a solution I introduced the kultarr package that also has options to visualise the created anchors as well.

To wrap up, these are the proposed geometric representations of the XAI methods in summary.

So far the examples that we gave were in two dimensions, but in the real world datasets will have a large number of features and to visualize this large number of features while also allowing the user to select the observations that they want to explain, we made some improvements to detourr.

Combining everything we have worked on the first year, we created a small shiny app to explore and understand a model, the data and the explanations all in one place. 

This shiny app was then packaged into an R package called Rosella, which is still under development. 

Now that's done, shall we join a cult. Everyone's talking about LLMs and deep learning these days. Let's have a raise of hands who spoke with an LLM already today? 

So earlier we talked about model agnostic XAI methods, but in reality when it comes to neural networks, simply prodding and poking the model wont tel you much about the model unless you open the model up and look at the internals

So let's fit a neural network, specifically for this dataset.

Now for a quick refresher on how neural networks are fitted.

A neural network is a combination of linear transformations where each is followed by a non linear function. We have a set of input features and a hidden layer and an output layer as the basic setup. A hidden layer consists of units called neurons which contain a weight for each of the unit of the previous layer and a bias variable which is added to create the output of the neuron. This output is then sent through a non linear function such as ReLU to ensure that the signal passes through a neuron once a certain threshold is reached. Now these weights are learned by performing a forward pass to obtain the prediction from the model and then comparing it with the ground truth to obtain a loss. This loss is then used to calculate the gradient with respect to each weight to have a sense of how much the loss is influenced by each weight. Then using a learning rate we update the weights iteratively through several epochs. And once that's set we let it run and et voila we have a neural network. Notice how I set arbitrary set of hyperparameters without thinking and the model learned the decision boundary somewhat well?  
That's something we are going to talk about soon.

Now back to where we left things at,

Let's fix some hyperparams for the plot, but I am a benovelent democratic presenter I will let everyone here and online to vote on how many neurons we should use for that dataset we just saw. Specifically this dataset.

Now based on the results we are going to see who got the best fit on the model.

So was this the result you wanted to see? Think again, there's more than answer to the result. All of these are possible models for the given neural network architecture

Let's take a second to look at it more intently, These are the worst performing models based on the F1 score for each of the neuron levels. Now let's take a look at the best of each. Notice how the best of 5 and 8 neurons is capable of being closely fitting as the 30 neuron model. But let's take a look at the entire spread. Now here's where my story comes into play. I was somewhere here. Basically, in the worst of the bunch. 

So any guesses what happened?

The answer is simple.

I was using the wrong random seed. I was fixing everything else in the model but never thought or saw anywhere that the random seed might have been the problem 

But how could that be? Well for starters the initial weights has an effect on the optimization problem. This has been mentioned in techniques such as MAML for few shot training where a neural network is trained to generalize among multiple tasks by trying to find a common initial weight.  

Well this was a fun story but why does it matter? 

I could have just chucked a 20 neuron model when it didn't work at the first instance and called it a day. I would be presenting something completely different today if I went with that. But take a look at this dataset. Do we really need 20 * 2 + 20 + 20 + 20 = 100 parameters when 5 * 2 + 5 + 5 + 5 = 25 parameters can do the job. More than 4 times more parameters 

So I went back to where most of the issues came from. We are not talking about how training neural networks are more like winning the lottery. Every LLM you see out there has to go through multiple restarts, better initializations and many more tricks to make sure that the result is a good working model. To make sure I raise awareness about this, I decided to start from the place that this needs to be talked about from, the educators. And for that I created this application and you get to be the second batch of students that experience this application. 

Things to talk about in the app.

The tutorial to get the students close to the concept about to be taught, the application divided into several parts. The squiggler tool allowing us to move and add new nodes. The ability to select neurons to test and how many replicates per neuron. Then the model fit monitoring. and the resulting spread, being able to select individual models and seeing the fit. seeing the gif of spread. 

The backend consists of multiple technologies as shown here. 

The methodology is quite simple, we made sure to generate the dataset using a fixed seed, and we randomly sampled (using cryptographically safe randomly generated) seeds between a wide range and used that to create replicates of the model. 

Now where to from here? 

We have probably heard of the attention is all you need paper a million times now, at least I have and but did we need this many parameters to work with llms? 

As the third and final project I aim to work on top of a toy transformer model to understand what are the internal circuits that a transformer learns through the training process? Does grokking happen etc. These days I'm working on developing transformers from scratch to obtain a more ground up understanding instead of using readily available models like gpt-2 

The time line looks like this

As it is tradition, the slide color palette was inspired by this photograph in the Hellenic Museum in Flagstaff here in Melbourne when I was filming a documentary there for a film festival during my break.

