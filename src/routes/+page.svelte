<script>
  import IntroContent from '../components/IntroContent.svelte';
  import PredictedDisctribution from '../components/PredictedDistribution.svelte';
  import ActualDistribution from '../components/ActualDistribution.svelte';
  import Content from '../components/Content.svelte';

</script>
<head>
  <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
</head>

<IntroContent />
<PredictedDisctribution />
<ActualDistribution />
<Content />
```
<style>
  :global(body) {
    font-family: 'Roboto', sans-serif;
  }

  :global(main) {
    max-width: 960px;
    margin: 0 auto;
    padding: 20px;
  }

  :global(p), :global(h1), :global(h2), :global(h3), :global(h4), :global(h5), :global(h6) {
    color: #333;
    line-height: 1.6;
  }

  :global(a) {
    color: #007BFF;
    text-decoration: none;
  }

  :global(a:hover) {
    color: #0056b3;
    text-decoration: underline;
  }
</style>