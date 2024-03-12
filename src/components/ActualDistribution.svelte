<script>
  import * as d3 from 'd3';
  import { onMount } from 'svelte';
  import centersData from './center.json';
  import pointGuardsData from './point_guard.json';

  let svg;
  let showDistribution = false;
  

  // Function to calculate mean
  function mean(data) {
    return d3.mean(data, d => +d.ReboundPct);
  }

  // Function to calculate standard deviation
  function standardDeviation(data, mean) {
    return Math.sqrt(d3.mean(data, d => Math.pow(+d.ReboundPct - mean, 2)));
  }

  // Function to generate Gaussian distribution points
  function gaussianPoints(mean, stdDev, xScale, height) {
    const points = [];
    for (let i = xScale.domain()[0]; i <= xScale.domain()[1]; i += (xScale.domain()[1] - xScale.domain()[0]) / 100) {
      const x = i;
      const y = (1 / (stdDev * Math.sqrt(2 * Math.PI))) * Math.exp(-0.5 * Math.pow((x - mean) / stdDev, 2));
      points.push({x: xScale(x), y: height - y * height * 5}); // Adjust scaling factor as needed
    }
    return points;
  }

  async function loadData() {
    const centers = centersData;
    const pointGuards = pointGuardsData;

    const data = centers.map(d => ({...d, type: 'center'}))
      .concat(pointGuards.map(d => ({...d, type: 'pointGuard'})));

    const margin = {top: 10, right: 30, bottom: 50, left: 40},
      width = 1000 - margin.left - margin.right,
      height = 500 - margin.top - margin.bottom;

    const x = d3.scaleLinear()
      .domain([0, d3.max(data, d => +d.ReboundPct)])
      .range([0, width]);
    
    const y = d3.scaleLinear()
      .domain([0, d3.max(data, d => +d.Index)])
      .range([height, 0]);

    svg = d3.select("#plinkoPlot")
      .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
      .append("g")
        .attr("transform", `translate(${margin.left},${margin.top})`);

    // Add the x-axis
    svg.append("g")
      .attr("transform", `translate(0,${height})`)
      .call(d3.axisBottom(x));

    

        // Add the points for centers and point guards, positioned on the x-axis
        svg.append("g")
      .selectAll("dot")
      .data(data)
      .enter()
      .append("circle")
        .attr("cx", d => x(+d.ReboundPct))
        .attr("cy", height) // Set y-coordinate to height to align with the x-axis
        .attr("r", 5)
        .style("fill", d => d.type === 'center' ? "#69b3a2" : "#404080");

    // Calculate mean and standard deviation for each dataset
    const centersMean = mean(centers);
    const centersStdDev = standardDeviation(centers, centersMean);
    const pointGuardsMean = mean(pointGuards);
    const pointGuardsStdDev = standardDeviation(pointGuards, pointGuardsMean);
    // Generate Gaussian points
    const centersGaussian = gaussianPoints(centersMean, centersStdDev, x, height);
    const pointGuardsGaussian = gaussianPoints(pointGuardsMean, pointGuardsStdDev, x, height);

    // Draw Gaussian lines for centers
    svg.append("path")
    .attr("class", "gaussian-line")
      .datum(centersGaussian)
      .attr("fill", "none")
      .attr("stroke", "#69b3a2")
      .attr("stroke-width", 1.5)
      .attr("d", d3.line()
          .x(d => d.x)
          .y(d => d.y)
      );

    // Draw Gaussian lines for point guards
    svg.append("path")
    .attr("class", "gaussian-line")
      .datum(pointGuardsGaussian)
      .attr("fill", "none")
      .attr("stroke", "#404080")
      .attr("stroke-width", 1.5)
      .attr("d", d3.line()
          .x(d => d.x)
          .y(d => d.y)
      );
  }
  function toggleDistribution() {
    showDistribution = !showDistribution;
    // Select the Gaussian paths and set their visibility based on the showDistribution state
    svg.selectAll(".gaussian-line")
       .style("display", showDistribution ? "block" : "none");
  }
  onMount(() => {
    loadData().then(() => {
      // Initially set the Gaussian paths to be hidden
      toggleDistribution();
    });
  });
</script>

<main>
  <button on:click={toggleDistribution}>
    {showDistribution ? 'Hide' : 'Show'} Distribution
  </button>
  <div id="plinkoPlot"></div>
</main>

<style>
  /* You can style your button here */
  button {
    /* Example style */
    padding: 0.5rem 1rem;
    font-size: 1rem;
    cursor: pointer;
  }
</style>
