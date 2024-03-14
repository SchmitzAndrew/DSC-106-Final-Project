<script>
  // Write your JS here, or import other files
</script>

<main>
  <h1>Maximum Likelihood Estimation</h1>

  <p>
    Have you ever wanted to be able to predict things in nature? Whether it is the species of an animal,
    roll of a die,  or anything that has a specific class. It might surprise you that you are able to 
    do this using some math you may already know. But there is one issue when it comes to predicting 
    outcomes of nature. A major drawback when it comes to predicting things in nature is that it’s rare 
    that we have the true distribution of where the data came from. Luckily there is a solution to this. 
    One approach that data scientists use is they assume the data comes from some parametric density given the data is continuous.
  </p>

  <p>
    To start Data Scientists first make an assumption that being that the true, underlying density has a 
    certain form. A common assumption used is that the true distribution is Gaussian or also known as 
    normal distribution. With this assumption there are two parameters that determine the shape of the 
    distribution being mean and standard deviation. The mean controls the center and the standard 
    deviation controls the width of the distribution. But because we do not know the true density we 
    have to make assumptions on the parameters. This allows us to make an estimation to what the true 
    distribution may look like.
  </p>

  <p>
    Below is data sampled from the NBA showing the average number of rebounds each player has gotten
    in the season. As you can see you are able to input your own Mean and Standard deviation. You will
    be playing the role as a Data Scientist in trying to make a model distribution that best fits the 
    data. Go ahead and see how well you do!!! Then whenever you feel confident in your answer, compare 
    yours with the best possible fit.
  </p>
</main>

<style>
  main h1 {
    text-align: center;
  }
</style>

