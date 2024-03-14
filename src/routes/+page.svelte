<script>
  import { onMount } from 'svelte';
  import IntroContent from '../components/IntroContent.svelte';
  import PredictedDistribution from '../components/PredictedDistribution.svelte';
  import ActualDistribution from '../components/ActualDistribution.svelte';
  import Content from '../components/Content.svelte';
  import ActualDistributionText from '../components/ActualDistributionText.svelte';

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
      <h2>More Intro</h2>
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
      <h2>Even More Intro</h2>
      <p>Below is data sampled from the NBA showing the average number of rebounds each player has gotten
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

  

  {#each [PredictedDistribution, ActualDistributionText, ActualDistribution, Content] as Component, index (index)}
    <section class="box" class:in-view={currentInView === index}>
      <svelte:component this={Component} />
    </section>
    
  {/each}

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

  .scrolling-container {
    scroll-snap-type: y mandatory;
    overflow-y: scroll;
    height: 100vh;
    background-color: #D3D3D3;
    padding-top: 50px;
  }

  .box {
    height: 100vh;
    width: 100%;
    scroll-snap-align: start;
    display: flex;
    justify-content: center;
    align-items: center;
    transform: scale(0.8);
    transition: transform 0.5s ease-in-out;
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
</style>
