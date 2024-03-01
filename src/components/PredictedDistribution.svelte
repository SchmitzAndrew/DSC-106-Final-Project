
<!-- NormalDistribution.svelte -->
<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  import { writable } from 'svelte/store';

  const width = 960;
  const height = 500;
  const radius = 2;
  const margin = { top: 10, right: 30, bottom: 30, left: 30 };
  const n = 2500; // number of points

  const mu = writable(0.4);
  const sigma = writable(0.1);

  let svg;

  const dodger = (radius) => {
    const radius2 = radius ** 2;
    const bisect = d3.bisector(d => d.x).left;
    const circles = [];
    return (x) => {
      let y = 0;
      let l = bisect(circles, x - radius, 0);
      let r = bisect(circles, x + radius, l);

      for (let i = l; i < r; ++i) {
        const xi = circles[i].x;
        const yi = circles[i].y;
        const x2 = (xi - x) ** 2;
        const y2 = (yi - y) ** 2;
        if (radius2 > x2 + y2) {
          y = yi + Math.sqrt(radius2 - x2) + 1e-6;
          i = l - 1; // restart scan
        }
      }
      circles.splice(bisect(circles, x, l, r), 0, { x, y });
      return y;
    };
  };

  function update(muValue, sigmaValue) {
    if (svg) {
      svg.selectAll("*").remove();
    } else {
      svg = d3.select("#chart").append("svg")
        .attr("width", width)
        .attr("height", height);
    }

    const dodge = dodger(radius * 2 + 1);
    const values = Float64Array.from({ length: n }, d3.randomNormal(muValue, sigmaValue));
    const x = d3.scaleLinear([0, 1], [margin.left, width - margin.right]);

    svg.append("g")
      .attr("transform", `translate(0,${height - margin.bottom})`)
      .call(d3.axisBottom(x));

    svg.selectAll("circle")
      .data(values)
      .join("circle")
      .attr("cx", d => x(d))
      .attr("cy", d => height - margin.bottom - dodge(x(d)) - radius)
      .attr("r", radius);
  }

  onMount(() => {
    const unsubscribeMu = mu.subscribe(muValue => {
      sigma.subscribe(sigmaValue => {
        update(muValue, sigmaValue);
      })();
    });

    return () => {
      unsubscribeMu();
    };
  });
</script>

<main>
  <div id="chart"></div>
  <label>
    mu: expected value
    <input type="range" min="0" max="1" step="0.001" bind:value={$mu}>
  </label>
  <label>
    sigma: standard deviation
    <input type="range" min="0" max="0.4" step="0.001" bind:value={$sigma}>
  </label>
</main>

<style>
  /* Your CSS styles */
  label {
    display: block;
    margin: 1em 0;
  }

  input[type=range] {
    width: 100%;
  }
</style>
