<!-- <script>
    import { onMount } from "svelte"
    import * as d3 from "d3"

    const data = [4, 12, 44, 32, 54, 65, 88]

    onMount(async () => {

        const div = d3.create("div")
            .style("font", "10px sans-serif")
            .style("text-align", "right")
            .style("color", "white")

        const colorScale = d3.scaleLinear()
            .domain([0, d3.max(data)])
            .range(["lightblue", "steelblue"])

        div.selectAll("div")
            .data(data)
            .join("div")
            .style("background", d => colorScale(d)) 
            .style("padding", "4px")
            .style("margin", "2px")
            .style("width", d => `${d * 10}px`)
            .text(d => d)
        
        document.querySelector(".chart-container").appendChild(div.node())

    })
</script>

<div class="chart-container"></div>

<style>
    .chart-container {
        height: 100vh;
        background-color: white;
        display: flex;
        justify-content: center;
        align-items: center;
    }
</style> -->
