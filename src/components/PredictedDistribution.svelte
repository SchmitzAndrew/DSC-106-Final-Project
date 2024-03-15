<script>
  import { onMount } from 'svelte';
import * as d3 from 'd3';
import { writable } from 'svelte/store';

const width = 950;
const height = 500;
const margin = { top: 10, right: 30, bottom: 30, left: 30 };

  const mu = writable(0);
  const sigma = writable(0);
  const minReboundPct = writable(0);
  const maxReboundPct = writable(1);



  const mu2 = writable(0);
  const sigma2 = writable(0);
  const minReboundPct2 = writable(0);
  const maxReboundPct2 = writable(1);
  let centerData; // This will store the random data points from the second CSV

let svg;
let x; // Will set these scales after SVG is created
let y;
let pointguardData; // This will store the random data points

// Modify the parsing function to only use 'ReboundPct'


  onMount(async () => {
    // Replace this with the actual path to your CSV file
   

    
    const centerRes = await fetch('/center.csv'); 

    const centerCsv = await centerRes.text();

    centerData = d3.csvParse(centerCsv, d3.autoType)
    console.log(centerData); // Check for any NaN values or unexpected formats

    

    let minPct = d3.min(centerData, d => d.ReboundPct);
    let maxPct = d3.max(centerData, d => d.ReboundPct);
    minReboundPct.set(minPct);
    maxReboundPct.set(maxPct);
    // Initialize mu and sigma with reasonable defaults
    mu.set((minPct + maxPct) / 2);
    sigma.set((maxPct - minPct) / 4); // for example

    svg = d3.select("#chart").append("svg")
      .attr("width", width)
      .attr("height", height);




     // Load the second CSV file
     
    const pgRes = await fetch('/point_guard.csv'); 

    const pgCsv = await pgRes.text();

    pointguardData = d3.csvParse(pgCsv, d3.autoType)
    console.log(pointguardData); // Similarly, check this dataset
    if (centerData.length > 0) {
  console.log("First entry:", centerData[0]);
  console.log("x value of the first entry:", centerData[0].x);
  console.log("y value of the first entry:", centerData[0].y);
}
if (centerData.length > 0) {
  console.log("Type of x:", typeof centerData[0].x);
  console.log("Type of y:", typeof centerData[0].y);
}

    // Calculate range and initial values for the second dataset
    let minPct2 = d3.min(pointguardData, d => d.ReboundPct);
    let maxPct2 = d3.max(pointguardData, d => d.ReboundPct);
    minReboundPct2.set(minPct2);
    maxReboundPct2.set(maxPct2);
    mu2.set((minPct2 + maxPct2) / 2);
    sigma2.set((maxPct2 - minPct2) / 4); // Adjust as needed

    // Initial update call to draw the charts
    update($mu, $sigma, $mu2, $sigma2);
  });

  function generateGaussianCurveData(mu, sigma, extentX, numPoints) {
  const data = [];
  const rangeX = extentX[1] - extentX[0]; // The range of your x-axis data
  const step = rangeX / numPoints; // Step size based on the actual data range
  for (let i = 0; i <= numPoints; i++) {
    let x = extentX[0] + i * step; // Start from the minimum x value
    let exponent = -Math.pow(x - mu, 2) / (2 * Math.pow(sigma, 2));
    let y = (1 / (sigma * Math.sqrt(2 * Math.PI))) * Math.exp(exponent);
    data.push(({ x: x, y: y }));
  }
  return data;
}






 // This function now just declares what should happen when mu and sigma change
 
 


 

 function update(muValue, sigmaValue, mu2Value, sigma2Value) {
  // Clean the svg before redrawing
  if (svg) svg.selectAll("*").remove();

  // Determine the extent for the x-axis based on both datasets
  let extentX;
  if (centerData && pointguardData) {
    const extentX1 = d3.extent(centerData, d => d.ReboundPct);
    const extentX2 = d3.extent(pointguardData, d => d.ReboundPct);
    extentX = [Math.min(extentX1[0], extentX2[0]), Math.max(extentX1[1], extentX2[1])];
  } else if (centerData) {
    extentX = d3.extent(centerData, d => d.ReboundPct);
  } else if (pointguardData) {
    extentX = d3.extent(pointguardData, d => d.ReboundPct);
  } else {
    // If neither dataset is available, return early
    return;
  }

  // Define scales
  x = d3.scaleLinear(extentX, [margin.left, width - margin.right]);
  const maxY = Math.max(
    1 / (sigmaValue * Math.sqrt(2 * Math.PI)),
    1 / (sigma2Value * Math.sqrt(2 * Math.PI))
  );
  y = d3.scaleLinear([0, maxY], [height - margin.bottom, margin.top]);

  // Draw the x-axis
  svg.append("g")
    .attr("transform", `translate(0,${height - margin.bottom})`)
    .call(d3.axisBottom(x));
   const legend = svg.append('g')
  .attr('class', 'legend')
  .attr('transform', `translate(${width - margin.right - 100}, ${margin.top})`);

  legend.append('rect')
  .attr('width', 18)
  .attr('height', 18)
  .attr('fill', '#404080');

legend.append('text')
  .attr('x', 24)
  .attr('y', 9)
  .attr('dy', '0.35em')
  .style('text-anchor', 'start')
  .text('Point Guards');

// Add the second legend item for centers
legend.append('rect')
  .attr('width', 18)
  .attr('height', 18)
  .attr('y', 30) // Offset the second legend item
  .attr('fill', '#69b3a2');

legend.append('text')
  .attr('x', 24)
  .attr('y', 39)
  .attr('dy', '0.35em')
  .style('text-anchor', 'start')
  .text('Centers');
  const line = d3.line()
  .x(d => x(d.ReboundPct)) // Ensure x(d.x) returns a number for all d
  .y(d => y(d.y)); // Similarly, ensure y(d.y) returns a number

  // Draw curves and points for the first dataset
  if (centerData) {
    const gaussianCurveData = generateGaussianCurveData(muValue, sigmaValue, extentX, 1000);
    svg.append("path")
      .datum(gaussianCurveData)
      .attr("fill", "none")
      .attr("stroke", "#69b3a2")
      .attr("d", line);

    svg.selectAll(".random-dot")
      .data(centerData)
      .enter().append("circle")
      .attr("class", "random-dot")
      .attr("cx", d => x(d.ReboundPct))
      .attr("cy", height - margin.bottom)
      .attr("r", 3)
      .style("fill", "#69b3a2");
  }

  // Draw curves and points for the second dataset
  if (pointguardData) {
    const gaussianCurveData2 = generateGaussianCurveData(mu2Value, sigma2Value, extentX, 1000);
    svg.append("path")
      .datum(gaussianCurveData2)
      .attr("fill", "none")
      .attr("stroke", "#404080")
      .attr("d", line);

    // You may want to use a different class or fill style for these points
    svg.selectAll(".random-dot2")
      .data(pointguardData)
      .enter().append("circle")
      .attr("class", "random-dot2")
      .attr("cx", d => x(d.ReboundPct))
      .attr("cy", height - margin.bottom)
      .attr("r", 3)
      .style("fill", "#404080");
  }
 
}
$: if (svg && (centerData || pointguardData)) {
    update($mu, $sigma, $mu2, $sigma2);
}


</script>


<main>
  <div id="chart"></div>
  
  <!-- Inputs for the first curve -->
  <label>
   mu (Curve 1): expected value
   <input type="number" bind:value={$mu} min={$minReboundPct} max={$maxReboundPct} step={($maxReboundPct - $minReboundPct) / 100}>
  </label>
  <input type="range" bind:value={$mu} min={$minReboundPct} max={$maxReboundPct} step={($maxReboundPct - $minReboundPct) / 100}>
 
  <label>
   sigma (Curve 1): standard deviation
   <input type="number" bind:value={$sigma} min="0" max={($maxReboundPct - $minReboundPct) / 2} step={($maxReboundPct - $minReboundPct) / 200}>
  </label>
  <input type="range" bind:value={$sigma} min="0" max={($maxReboundPct - $minReboundPct) / 2} step={($maxReboundPct - $minReboundPct) / 200}>
  
  <!-- Inputs for the second curve -->
  <label>
   mu (Curve 2): expected value
   <input type="number" bind:value={$mu2} min={$minReboundPct2} max={$maxReboundPct2} step={($maxReboundPct2 - $minReboundPct2) / 100}>
  </label>
  <input type="range" bind:value={$mu2} min={$minReboundPct2} max={$maxReboundPct2} step={($maxReboundPct2 - $minReboundPct2) / 100}>
 
  <label>
   sigma (Curve 2): standard deviation
   <input type="number" bind:value={$sigma2} min="0" max={($maxReboundPct2 - $minReboundPct2) / 2} step={($maxReboundPct2 - $minReboundPct2) / 200}>
  </label>
  <input type="range" bind:value={$sigma2} min="0" max={($maxReboundPct2 - $minReboundPct2) / 2} step={($maxReboundPct2 - $minReboundPct2) / 200}>
 </main>

<style>
 /* Your CSS styles */
 label {
   display: block;
   margin: 1em 0;
 }

 input[type=range],
 input[type=number] {
   width: 100%;
 }
</style>