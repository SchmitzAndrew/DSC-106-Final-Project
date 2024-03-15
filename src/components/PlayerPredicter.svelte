<script>
    import * as d3 from 'd3';
    import { onMount } from 'svelte';
  
    let svg;
   
    let inputValue = 0; // To hold the user's input value

    let x;
    let height;
   

    const PY1 = 0.51; // Replace with your actual value for P(Y=1)
    const PY0 = 0.49; // Replace with your actual value for P(Y=0)
    let predictionText = '';
    let centersMean, centersStdDev, pointGuardsMean, pointGuardsStdDev;
    // Function to calculate mean
    function mean(data) {
      return d3.mean(data, d => +d.ReboundPct);
    }
  
    // Function to calculate standard deviation
    function standardDeviation(data, mean) {
      return Math.sqrt(d3.mean(data, d => Math.pow(+d.ReboundPct - mean, 2)));
    }
  
    // Function to generate Gaussian distribution points
    function gaussianPoints(mean, stdDev, xScale, height, multiplier) {
  const points = [];
  for (let i = xScale.domain()[0]; i <= xScale.domain()[1]; i += (xScale.domain()[1] - xScale.domain()[0]) / 100) {
    const x = i;
    const y = multiplier * (1 / (stdDev * Math.sqrt(2 * Math.PI))) * Math.exp(-0.5 * Math.pow((x - mean) / stdDev, 2));
    points.push({x: xScale(x), y: height - y * height * 10});
  }
  return points;
}
    let isLoadingData = true;
    async function loadData() {
      const centers = await d3.csv('/center.csv');
      const pointGuards = await d3.csv('/point_guard.csv');
  
      const data = centers.map(d => ({...d, type: 'center'}))
        .concat(pointGuards.map(d => ({...d, type: 'pointGuard'})));
  
        const margin = {top: 10, right: 30, bottom: 5, left: 40},
            width = 1000 - margin.left - margin.right;
      height = 500 - margin.top - margin.bottom; // Assign height here

      x = d3.scaleLinear() // Assign x scale here
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
      centersMean = mean(centers);
        centersStdDev = standardDeviation(centers, centersMean);
        pointGuardsMean = mean(pointGuards);
        pointGuardsStdDev = standardDeviation(pointGuards, pointGuardsMean);
      // Generate Gaussian points
      const centersGaussian = gaussianPoints(centersMean, centersStdDev, x, height, PY1);
      const pointGuardsGaussian = gaussianPoints(pointGuardsMean, pointGuardsStdDev, x, height, PY0);

      const maxProbability = Math.max(
        d3.max(centersGaussian, d => d.y),
        d3.max(pointGuardsGaussian, d => d.y)
    );

    const yProbabilityScale = d3.scaleLinear()
        .domain([0, maxProbability])
        .range([height, 0]);
  
      // Draw Gaussian lines for centers
      svg.append("path")
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
        .datum(pointGuardsGaussian)
        .attr("fill", "none")
        .attr("stroke", "#404080")
        .attr("stroke-width", 1.5)
        .attr("d", d3.line()
            .x(d => d.x)
            .y(d => d.y)
        );

        const yAxisOffset = margin.left - 40;  // Adjust the value as needed to shift the axis left

        svg.append("g")
        .attr("class", "y-axis")
        .attr("transform", `translate(${yAxisOffset}, 0)`)
        .call(d3.axisLeft(yProbabilityScale).tickFormat(d3.format(".2f"))); // Format ticks to scientific notation if needed

        isLoadingData = false;

addLegend(svg, width - margin.left - margin.right, height - margin.top - margin.bottom);
    }
  
    function drawInputLine() {
  // Ensure x and height are used correctly here
  inputValue = +inputValue; // Make sure inputValue is a number
  const xPosition = x(inputValue);
  
  svg.selectAll(".input-line").remove();

  svg.append("line")
    .attr("class", "input-line")
    .attr("x1", xPosition)
    .attr("y1", 0)
    .attr("x2", xPosition)
    .attr("y2", height)
    .attr("stroke-width", 2)
    .attr("stroke", "red");

    console.log('inputValue:', inputValue);
  console.log('Means and StDevs:', centersMean, centersStdDev, pointGuardsMean, pointGuardsStdDev);

  // Calculate the Gaussian probability density for the input value
  const centersPDF = gaussianPDF(inputValue, centersMean, centersStdDev);
  const pointGuardsPDF = gaussianPDF(inputValue, pointGuardsMean, pointGuardsStdDev);
  console.log('centersPDF:', centersPDF, 'pointGuardsPDF:', pointGuardsPDF);

  if (!isNaN(centersMean) && !isNaN(centersStdDev) && !isNaN(pointGuardsMean) && !isNaN(pointGuardsStdDev)) {
    const centersPDF = gaussianPDF(inputValue, centersMean, centersStdDev);
    const pointGuardsPDF = gaussianPDF(inputValue, pointGuardsMean, pointGuardsStdDev);



  // Apply Bayes' theorem to calculate the posterior probabilities
  const postProbCenter = centersPDF * PY1 / (centersPDF * PY1 + pointGuardsPDF * PY0);
  const postProbPointGuard = pointGuardsPDF * PY0 / (centersPDF * PY1 + pointGuardsPDF * PY0);
  console.log('postProbCenter:', postProbCenter, 'postProbPointGuard:', postProbPointGuard);

  // Display the prediction based on which probability is higher
  predictionText = postProbCenter > postProbPointGuard ? 'Center' : 'Point Guard';
 

  }else {
    console.log('Data is not yet loaded, or there is an issue with the dataset.');
  }
}

function addLegend(svg, width, height) {
  // Define the legend
  const legendXOffset = 100; // Change this value as needed to fit your legend within the canvas
  const legendYOffset = 20;  // Adjust this if you also need to reposition vertically

  const legend = svg.append("g")
    .attr("class", "legend")
    .attr("transform", "translate(" + (width - legendXOffset) + "," + legendYOffset + ")");

  // Legend for Point Guard
  legend.append("rect")
    .attr("width", 18)
    .attr("height", 18)
    .style("fill", "#404080");

  legend.append("text")
    .attr("x", 24)
    .attr("y", 9)
    .attr("dy", ".35em")
    .style("text-anchor", "start")
    .text("P(X=x|Y=Point Guard)");

  // Legend for Center
  legend.append("rect")
    .attr("y", 30) // This moves the second legend entry down
    .attr("width", 18)
    .attr("height", 18)
    .style("fill", "#69b3a2");

  legend.append("text")
    .attr("x", 24)
    .attr("y", 39) // Align text with the second legend entry
    .attr("dy", ".35em")
    .style("text-anchor", "start")
    .text("P(X=x|Y=Center)");
}

// Function to calculate the Gaussian probability density
function gaussianPDF(x, mean, stdDev) {
  return (1 / (stdDev * Math.sqrt(2 * Math.PI))) * Math.exp(-0.5 * Math.pow((x - mean) / stdDev, 2));
}
  
    onMount(() => {
      loadData();
    });
    

  </script>
  
  <main>
    <div class="controls">
      <input type="number" bind:value={inputValue} placeholder="Enter a data point" />
      <button on:click={drawInputLine} disabled={isLoadingData}>Submit</button>
      <div id="predictionResult">Prediction: <span>{predictionText}</span></div>
    </div>
    <div id="plinkoPlot"></div>
  </main>
  
  <style>
    /* Style as needed */
    .input-line {
    stroke-dasharray: 4;
  }

  .controls {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-bottom: 0em; /* Adjust this value to reduce space between controls and the plot */
  }

  #plinkoPlot {
    margin-top: 0; /* Set to 0 to remove any additional space */
  }

  input, button {
    width: 50%; /* Full width if you want */
    margin-bottom: 0.5em; /* Space between input fields and button */
  }

  #predictionResult {
    margin-top: 0.5em; /* Space between button and prediction result */
  }
  
  </style>