<script>
  import * as d3 from 'd3';
  import { onMount } from 'svelte';

  let svg;

  async function loadData() {
    const centers = await d3.csv('/center.csv');
    const pointGuards = await d3.csv('/point_guard.csv');

    const data = centers.map(d => ({...d, type: 'center'}))
      .concat(pointGuards.map(d => ({...d, type: 'pointGuard'})));

    const margin = {top: 10, right: 30, bottom: 50, left: 40}, // Increase bottom margin for label
      width = 1000 - margin.left - margin.right,
      height = 400 - margin.top - margin.bottom;

    const x = d3.scaleLinear()
      .domain([0, d3.max(data, d => +d.ReboundPct)]) // Use ReboundPct for x-axis
      .range([0, width]);
    
    const y = d3.scaleLinear()
      .domain([0, d3.max(data, d => +d.Index)]) // Assuming Index is used for y-axis
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

    // Add x-axis label
    svg.append("text")             
      .attr("transform", `translate(${width / 2}, ${height + margin.top + 20})`)
      .style("text-anchor", "middle")
      .text("Number of Rebounds");

    // Add the points
svg.append("g")
  .selectAll("dot")
  .data(data)
  .enter()
  .append("circle")
    .attr("cx", d => x(+d.ReboundPct))
    .attr("cy", height) // Set y to the bottom of the plot area
    .attr("r", 5)
    .style("fill", d => d.type === 'center' ? "#69b3a2" : "#404080");
  }

  onMount(() => {
    loadData();
  });
</script>

<main>
  <div id="plinkoPlot"></div>
</main>

<style>
  /* Style as needed */
</style>