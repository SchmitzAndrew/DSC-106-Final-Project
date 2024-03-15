<script>
  import { onMount } from 'svelte';
  import IntroContent from '../components/IntroContent.svelte';
  import PredictedDistribution from '../components/PredictedDistribution.svelte';
  import ActualDistribution from '../components/ActualDistribution.svelte';
  import Content from '../components/Content.svelte';
  import ActualDistributionText from '../components/ActualDistributionText.svelte';
  import PlayerPredicter from '../components/PlayerPredicter.svelte';


  let sections = [];
  let currentInView = -1;

  function scrollToSection(index) {
    sections[index].scrollIntoView({ behavior: 'smooth' });
  }

  onMount(() => {
    sections = document.querySelectorAll('.box');
    const observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          currentInView = parseInt(entry.target.dataset.index, 10);
        }
      });
    }, { threshold: 0.5 });

    sections.forEach((section, index) => {
      // Ensure the data-index is set here to match the section
      section.dataset.index = index;
      observer.observe(section);
    });
    svg = d3.select("#chart").append("svg")
    .attr("viewBox", `0 0 ${width} ${height}`) // Add this line
    .attr("preserveAspectRatio", "xMidYMid meet") // Add this line
    .attr("width", "100%") // Adjust to "100%" for responsiveness
    .attr("height", "100%"); // Adjust to "100%" for responsiveness

    return () => {
      observer.disconnect();
    };
  });
</script>


<div class="scrolling-container">

  <section class="box" data-index={0}>
    <div class="text-content">
      <h2>Maximum Likelihood Estimation</h2>
      <p>Have you ever wanted to be able to predict things in nature? Whether it is the species of an animal,
        roll of a die,  or anything that has a specific class. It might surprise you that you are able to 
        do this using some math you may already know. But there is one issue when it comes to predicting 
        outcomes of nature. A major drawback when it comes to predicting things in nature is that it’s rare 
        that we have the true distribution of where the data came from. Luckily there is a solution to this. 
        One approach that data scientists use is they assume the data comes from some parametric density given the data is continuous.</p>
        <img src="/gaussian.png" alt="Descriptive text about the image" class="mle-image"/>
      <!-- More paragraphs as needed -->
    </div>
  </section>

  <section class="box" data-index={1}>
    <div class="text-content">
      <h2>Thinking as a Data Scientist</h2>
      <p>To start Data Scientists first make an assumption that being that the true, underlying density has a 
        certain form. A common assumption used is that the true distribution is Gaussian or also known as 
        normal distribution. With this assumption there are two parameters that determine the shape of the 
        distribution being mean and standard deviation. The mean controls the center and the standard 
        deviation controls the width of the distribution. But because we do not know the true density we 
        have to make assumptions on the parameters. This allows us to make an estimation to what the true 
        distribution may look like.</p>
      <!-- More paragraphs as needed -->
    </div>
  </section>


  <section class="box" data-index={2}>
    <div class="text-content">
      <h2>Looking at Real Data</h2>
      <p>Below is data sampled from the NBA showing the average number of rebounds center and points guards gotten
        in the season. As you can see you are able to input your own Mean and Standard deviation. You will
        be playing the role as a Data Scientist in trying to make a model distribution that best fits the 
        data. Go ahead and see how well you do!!! Then whenever you feel confident in your answer, compare 
        yours with the best possible fit.</p>
        <img src="/nba-logo-transparent.png" alt="Descriptive text about the image" class="nba-image"/>
      <!-- More paragraphs as needed -->
    </div>
  </section>

  <section class="box" data-index={3}>
    <div class="text-content">
      <p> In the following visualization, you will be able to find the rebound percentage for point guards in purple and 
      the rebound percentage for center players in light blue. By looking at their distribution in the x-axis,
      try to guess how their Gaussian distributions would look like.</p>
      <!-- More paragraphs as needed -->
    </div>
  </section>

  

  {#each [PredictedDistribution] as Component, index (index)}
    <section class="box" class:in-view={currentInView === index}>
      <svelte:component this={Component} />
    </section>
  {/each}

  <section class="box" data-index={5}>
    <div class="text-content">
      <p> Let's see how your prediction for the distributions of these two positions and their corresponding 
        rebound percenatge compares to the actual distributions. Click on "Show Distribution" to reveal
        the true distributions.</p>
      <!-- More paragraphs as needed -->
    </div>
  </section>

  {#each [ActualDistribution] as Component, index (index)}
    <section class="box" class:in-view={currentInView === index}>
      <svelte:component this={Component} />
    </section>
  {/each}

  <section class="box" data-index={10}>
    <div class="text-content">
      <p> 
      </p>
      <!-- More paragraphs as needed -->
    </div>
  </section>

  {#each [PlayerPredicter] as Component, index (index)}
    <section class="box" class:in-view={currentInView === index}>
      <svelte:component this={Component} />
    </section>
  {/each}

  <section class="box" data-index={12}>
    <div class="text-content">
      <p> Now that you've looked at both distributions, in the visualization above input a "Rebound
        Percentage". Using MLE, our visualization will tell you which distribution your player's statistics would belong to.
      </p>
      <!-- More paragraphs as needed -->
    </div>
  </section>

  <section class="box" data-index={13}>
    <div class="text-content">
      <p> As you messed around with different parameter inputs you may have observed some inputs gave better
        results than others. Is there a way to quantify this without guessing and checking? Luckily ,
         Data Scientists have figured out how to quantify this step to find the “best fit”. First step 
         is to assume the data are all sampled independently. Next let's say that p(xi, mu, sigma) is
          the probability of seeing xi with the parameters mu and sigma. Then p(x1, mu, sigma) *  p(x2, mu, sigma) *... * p(xn, mu, sigma) 
          tells us the likelihood of seeing X1, x2,..., xn at the sametime. We can think of this as a function of mu and sigma. 
          The likelihood function takes in a mu and sigma and returns the probability that data was generated by the inputted mu and sigma.</p>
      <!-- More paragraphs as needed -->
    </div>
  </section>

  <section class="box" data-index={14}>
    <div class="text-content">
      <p> The goal of creating this likelihood function is to maximize it by finding the mu and sigma that 
        gives us the highest likelihood. One way is to take the partial derivatives, set them to 0 then 
        solve for mu and sigma.</p>
      <!-- More paragraphs as needed -->
    </div>
  </section>

  <section class="box" data-index={14}>
    <div class="text-content">
      <p>
        After you get through all the matches you are left with the equations below. For those who are interested in the full derivations you can view them
        <a
          href="https://medium.com/swlh/gaussian-distribution-and-maximum-likelihood-estimate-method-step-by-step-e4f6014fa83e"
          >here.</a>
      </p>
      <img src="/equations.png" alt="Descriptive text about the image" class="mle-image"/>
      <!-- More paragraphs as needed -->
    </div>
  </section>

  <section class="box" data-index={14}>
    <div class="text-content">
      <p> The mu maximum likelihood estimator is just the mean of the sampled data. And the sigma maximum likelihood estimator is the standard deviation of data but using the mu mle.
        By calculating these parameters from the data we can now fit a gaussian that best fits our data.
    </p>
      <!-- More paragraphs as needed -->
    </div>
  </section>
  
  <section class="box" data-index={14}>
    <div class="text-content">
      <p> Ok but I know you are wondering how this helps you predict things in nature. Well we can use this to estimate probability densities. But how? Well, our example of figuring how to predict whether a NBA player is a point guard or forward by the number of rebounds can give us insight on how it is done. We will create two separate fitted gaussians for each position. So we will have P(X| Y = PG) and P(X| Y = Forward). Then multiple each of those probabilities by P(Y=PG) and p(Y=Forward) respectively. Now with those gaussians how would we predict the position of a player? Well we will see which probability is greater at x.
      </p>
      <!-- More paragraphs as needed -->
    </div>
  </section>

  <section class="box" data-index={14}>
    <div class="text-content">
      <p> Some of you who are more familiar with statistics and math may wonder why would someone estimate the densities 𝑝𝑋 (𝑥 | 𝑌 = 0) and 𝑝𝑋 (𝑥 | 𝑌 = 1) using parametrically instead of using a non-parametric approach like using histograms. Well one reason is that histograms suffer from the curse of dimensionality meaning when there are more dimensions (aka. features) histograms will require significantly much more data to have a good fit. Meaning if the sample is low and you can make a fair assumption about the underlying true density of the data then a parametric approach might suit you better. Predicting nature is a very complicated process, one that we may never fully understand or be able to do but by having a variety of tools it allows us to do our best and perhaps one day we may get really close to predict nature.
      </p>
      <!-- More paragraphs as needed -->
    </div>
  </section>


</div>

<style>
  .navigation {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    display: flex;
    justify-content: center;
    gap: 10px;
    background-color: #f8f8f8;
    padding: 10px 0;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    z-index: 1000;
  }

  .navigation button {
    font-size: 18px;
    padding: 5px 10px;
    border: none;
    background-color: #e7e7e7;
    cursor: pointer;
    transition: background-color 0.3s;
  }

  .navigation button:hover {
    background-color: #dcdcdc;
  }



  .box.in-view {
    transform: scale(1);
  }

  /* Increase font size in IntroContent and Content components */
  .IntroContent p, .Content p {
    font-size: 20px; /* Adjust the size as needed */
  }
  .text-content h2 {
    font-size: 40px;
    margin-bottom: 16px;
    text-align: center;
  }

  .text-content p {
    font-size: 30px; /* Adjust the size as needed */
    padding: 0 20px; /* Add some padding around the text */
  }
  .mle-image {
  max-width: 50%; /* Ensures the image is not wider than the container */
  height: auto; /* Maintains the aspect ratio of the image */
  display: block; /* Renders the image as a block-level element */
  margin: 0 auto; /* Centers the image within the section */
}
.nba-image {
  max-width: 15%; /* Ensures the image is not wider than the container */
  height: auto; /* Maintains the aspect ratio of the image */
  display: block; /* Renders the image as a block-level element */
  margin: 0 auto; /* Centers the image within the section */
}
.scrolling-container {
  overflow-y: auto; /* Allows for normal scrolling */
  background-color: #D3D3D3;
}

.box {
  margin-bottom: 2rem; /* Adds space between sections */
  padding: 1rem; /* Additional padding within each section */
}

/* Remove the last-child margin-bottom if not needed */
.box:last-child {
  margin-bottom: 0;
}
</style>
