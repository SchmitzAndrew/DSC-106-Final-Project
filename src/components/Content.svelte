<script>
  import { onMount } from 'svelte';
  import IntroContent from '../components/IntroContent.svelte';
  import PredictedDistribution from '../components/PredictedDistribution.svelte';
  import ActualDistribution from '../components/ActualDistribution.svelte';
  import Content from '../components/Content.svelte';

  let sections = [];
  let currentInView = -1;

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
      section.dataset.index = index;
      observer.observe(section);
    });

    return () => {
      observer.disconnect();
    };
  });
</script>

<div class="scrolling-container">
  {#each [IntroContent, PredictedDistribution, ActualDistribution, Content] as Component, index (index)}
    <section class="box" class:in-view={currentInView === index}>
      <svelte:component this={Component} />
    </section>
  {/each}
</div>

<style>
  .scrolling-container {
    scroll-snap-type: y mandatory;
    overflow-y: scroll;
    height: 100vh;
  }

  .box {
    height: 100vh;
    width: 100%;
    scroll-snap-align: start;
    display: flex;
    justify-content: center;
    align-items: center;
    transform: scale(0.8); /* Start smaller */
    transition: transform 0.5s ease-in-out;
  }

  .box.in-view {
    transform: scale(1); /* Scale up when in view */
  }

  /* The rest of your global styles... */
</style>
