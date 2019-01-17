# Quadcopter_RL_DDPG

Use Deep Deterministic Policy Gradients with Actor-Critic, Ornstein-Uhlenbeck Noise, and Replay Buffer to teach Quadcoptoer how to reach a target location from ground position, idle, with no moving parts initially.

I spent quite alot of hours to tune hyperparameters and design proper neural networks for Actor and Critic models to yield a satisfying result.
Refer to below QnAs to gain insights into how I designed the algorithm and challenges I encountered (Open "Quadcopter_Project.html" or "Quadcopter_Project.ipynb" for references to graphs.

Question 1:

    Describe the task that you specified in task.py. How did you design the reward function?
Answer:

    -Goal : Quadcopter teaches itself to take off to a specified height from sitting idel on the ground with no moving parts as initial values.
    -Distance : Instead of using given (abs(self.sim.pose[:3] - self.target_pos)).sum(), I used distance formula ((x1-x2)^2 + (y1-y2)^2 + (z1-z2)^2)^0.5 which I thought was more accurate metric.
    -Reward : I used reward = np.tanh(2-(0.02*distance)) as reward function. This particular reward limits reward between (-1,1) and helped learning by stabilizing gradient update process.

Question 2: 

    Discuss your agent briefly, using the following questions as a guide:
    What learning algorithm(s) did you try? What worked best for you?
    What was your final choice of hyperparameters (such as αα, γγ, ϵϵ, etc.)?
    What neural network architecture did you use (if any)? Specify layers, sizes, activation functions, etc.
Answer

    -I used DDPG (Deep Deterministic Policy Gradients) which is an Actor-Critic method. On top of that, Ornstein-Uhlenbeck Noise and Replay Buffer were sprinkled in to improve performance.
        -Actor-Critic Method : Neural Networks were utilized to learn necessary weights. This method allows more flexibility since policy update(actor) and value update(critic) are separated. This separation decouples the parameters that are updated and produces target values.
        -Ornstein-Uhlenbeck Noise : Introduce random samples(noises) from Gaussian distribution, but each samples are closer. This helps in our Quadcopter setting, since we want random noise for exploration, but we don't want the Quadcoptor to fly like an UFO.
        -Replay Buffer - Stores experiences in tuples and samples stored values at random, and this helps us break the correlation and prevent action values from oscillating or diverging catastrophically.
    -Final Parameters:
        -buffer_size for Replay Buffer = 1000000
        -batch_size for Replay Buffer = 128 <- 2,4,8,16,64,128,256,,,, allows quicker iteration in modern computer memories
        -gamma for Q_target updates= 0.99 <- High gamma so that agent is interested in the distant future's rewards as well
        -tau for weight updates = 0.001 <- Put more emphasis on newly learned weights for weight updates
    -Neural Network was used for both Actor and Critic :
        -Actor
            -(Input Layer)(Dense Layer with 128 units)(Regularizer)(Batch Normalization)(Relu Activation)(Dropout 0.2)
            -L2 regularizer : 0.01 <- Help penalize exploding gradients and prevent overfitting by allowing generalization.
            -Batch Normalization : Optimizes training NN by reducing covariate shift by providing NN with inputs that are zero mean/unit variance.
            -Relu Activation is way to go. It helps NN to focus on nodes that are relevant by dropping negative nodes.
            -Dropout : 0.2 <- Prevents overfitting by utilizing different NN nodes instead of using specific ones over and over
            -Optimizer(Adam) Learning Rate : 0.01 <- I found this learning rate to be effective at algorithm converging the quickest
        -Critic
            -Very similar to those of Actor
            -Optimizer(Adam) Learning Rate : 0.001 <- I found this learning rate to be effective at algorithm converging the quickest

Question 3: 

    Using the episode rewards plot, discuss how the agent learned over time.
    -Was it an easy task to learn or hard?
    -Was there a gradual learning curve, or an aha moment?
    -How good was the final performance of the agent? (e.g. mean rewards over the last 10 episodes)

Answer:

    -It was pretty challenging initially to find right reward function, hyperparameter, NN layer structures, and etc to yield satisfying result. Everything was important, but I would say designing robust and working NN layer structure was the most important step in this project.
    -As seen in Episode Rewards vs Episode graph below, it followed step change in learning the right weights. Once the algorithm gradually shifted and found weights that worked, it quickly learned weights that yielded almost maximum reward toward the end.
    -Final performance of the agent improved significantly over time. It started out as having fairly low and instable episode rewards per episode. However, as the agent started learning the right weights, average episode reward increased until it capped out at around 76.306 over last 30 episodes. Also, graph of average x,y, and z distance over entire episodes shows that the agent learned to perform a vertical takeoff. Agent's average location for (x,y,z) stayed very close to target location (0,0,50) for each episode.


Question 4: 

    Briefly summarize your experience working on this project. You can use the following prompts for ideas.
    -What was the hardest part of the project? (e.g. getting started, plotting, specifying the task, etc.)
    -Did you find anything interesting in how the quadcopter or your agent behaved?

Answer:

    -Single-handedly the most difficult task of this project was defining and designing working Neural Network structures for Actor and Critic models. This was the case because Actor and Critic models were the core driving engines of learning optimal weights, besides reward function, to generate action values that will enable Quadcopter to behave in desired manner. Also, tuning hyperparameters were difficult as well since I lacked deep understanding of their effects on overall performance. However, I was able to quickly able to learn to tune the hyperparameters through trial-and-error and research.
    -Teaching Quadcopter to learn how to reach target location, and RL problems in general, was very interesting since it was analogous to teaching a real person to learn a new task. With right tools (hyperparameters), guidance (reward function), and curriculum (structures) the Quadcopter was able to learn what I wanted to teach, which was very similar to how people learn.



