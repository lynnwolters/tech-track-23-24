<!-- <script>
  import { onMount } from "svelte"
  import { select, scaleLinear, scaleBand, max } from "d3"

  const data = [10, 20, 30, 40, 50]

  onMount(async () => {
    // Set up the SVG container
    const svg = select("#chart")
      .append("svg")
      .attr("width", 400)
      .attr("height", 200)

    // Create scales
    const xScale = scaleBand()
      .domain(data.map((d, i) => i))
      .range([0, 400])
      .padding(0.1)

    const yScale = scaleLinear()
      .domain([0, max(data)])
      .range([0, 200])

    // Create and render bars
    svg.selectAll("rect")
      .data(data)
      .enter()
      .append("rect")
      .attr("x", (d, i) => xScale(i))
      .attr("y", (d) => 200 - yScale(d))
      .attr("width", xScale.bandwidth())
      .attr("height", (d) => yScale(d))
      .attr("fill", "steelblue")
  })
</script>

<div id="chart"></div>

 -->
