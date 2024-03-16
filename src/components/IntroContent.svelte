<script>
  // Write your JS here, or import other files
</script>

<main>
  <h1>Maximum Likelihood Estimation</h1>

  <p>
    Have you ever wanted to predict phenomena in nature? Whether it's identifying the species of an animal,
    the roll of a die, or categorizing objects into different groups, it might surprise you that 
    you can achieve this using some mathematics you may already be familiar with. However, there is a significant challenge when it comes to predicting 
    natural outcomes. A major drawback is that it’s rare 
    to have the true distribution of the data's origin. Fortunately, there is a solution to this problem. 
    One approach that data scientists use is to assume the data originates from a certain parametric density, provided the data is continuous.
  </p>

  <p>
    To begin, data scientists first assume that the true, underlying density has a 
    specific form. A common assumption is that the true distribution is Gaussian, also known as 
    the normal distribution. With this assumption, there are two parameters that determine the shape of the 
    distribution: the mean and the standard deviation. The mean controls the center, and the standard 
    deviation controls the width of the distribution. However, because we do not know the true density, we 
    must make assumptions about the parameters. This allows us to estimate what the true 
    distribution might look like.
  </p>

  <p>
    Below is data sampled from the NBA, showing the average number of rebounds each player has obtained
    in the season. Using our website, you  can input your own mean and standard deviation. You will
    play the role of a Data Scientist, attempting to create a model distribution that best fits the 
    data. Go ahead and see how well you do! Then, whenever you feel confident in your answer, compare 
    it with the best possible fit.
  </p>
</main>

<style>
  main h1 {
    text-align: center;
  }
</style>

