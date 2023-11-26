<script>
    import { onMount } from 'svelte'
    import * as d3 from 'd3'

    let radarChart

    const data = [
        { axis: 'Category A', value: 0.8 },
        { axis: 'Category B', value: 0.45 },
        { axis: 'Category C', value: 0.6 },
        { axis: 'Category D', value: 0.25 },
        { axis: 'Category E', value: 0.7 }
    ]

    onMount(async function() {
        createRadarChart(data)
    })

    function createRadarChart(data) {
        const width = 400
        const height = 400
        const margin = { top: 50, right: 50, bottom: 50, left: 50 }

        const maxValue = Math.max(...data.map(d => d.value))

        const angleSlice = (Math.PI * 2) / data.length

        const svg = d3.select(radarChart)
            .append('svg')
            .attr('width', width + margin.left + margin.right)
            .attr('height', height + margin.top + margin.bottom)
            .append('g')
            .attr('transform', `translate(${margin.left + width / 2},${margin.top + height / 2})`)

        const radius = d3.scaleLinear()
            .domain([0, maxValue])
            .range([0, Math.min(width / 2, height / 2)])

        const line = d3.lineRadial()
            .angle((d, i) => i * angleSlice)
            .radius(d => radius(d.value))

        svg.append('path')
            .datum(data)
            .attr('class', 'radar-chart-area')
            .attr('d', line)
            .attr('fill', 'rgba(0, 0, 255, 0.3)')

        svg.append('path')
            .datum(data)
            .attr('class', 'radar-chart-line')
            .attr('d', line)
            .attr('stroke', 'blue')
            .attr('fill', 'none')
            .attr('stroke-width', 2)

        const circleRadius = [0.2, 0.4, 0.6, 0.8, 1]

        circleRadius.forEach(r => {
            svg.append('circle')
                .attr('cx', 0)
                .attr('cy', 0)
                .attr('r', radius(maxValue) * r)
                .attr('class', 'radar-chart-circle')
                .attr('stroke', 'gray')
                .attr('fill', 'none')
                .attr('stroke-dasharray', '2,2')
        })

        const labels = svg.selectAll('.radar-chart-label')
            .data(data)
            .enter().append('text')
            .attr('class', 'radar-chart-label')
            .attr('x', (d, i) => radius(maxValue) * Math.cos(angleSlice * i - Math.PI / 2))
            .attr('y', (d, i) => radius(maxValue) * Math.sin(angleSlice * i - Math.PI / 2))
            .text(d => d.axis)
            .attr('text-anchor', 'middle')
            .attr('dy', '0.35em')
    }
</script>


<section>
    
    <div>
        <h2 class="title-normal">The Cause</h2>
    </div>
    
    <div>
        <p class="p-text-normal">Reasons why people didn’t take security measurements</p>
        <div>
            <button class="p-text-small">All</button>
            <button class="p-text-small">Reason 1</button>
            <button class="p-text-small">Reason 2</button>
            <button class="p-text-small">Reason 3</button>
        </div> 
        <div bind:this={radarChart}></div> 
    </div>
    
    <div>
        <h2 class="title-small">Recap</h2>
        <p class="p-text-normal">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. <br> <br> Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum. Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi.</p>
    </div>

</section>

<style>
    /* TITLE */
    section > div:nth-of-type(1) {
        margin: 0 2em 12em 2em;
    }

    section > div:nth-of-type(1) h2 {
        text-align: center;
    }

    /* CHART */
    section div:nth-of-type(2) {
        display: flex;
        flex-direction: column;
        align-items: center;
        margin: 0 2em 12em 2em;
    }

    section div:nth-of-type(2) div:nth-of-type(1)  {
        display: flex;
    }

    section div:nth-of-type(2) div:nth-of-type(1) button  {
        background: var(--color-3);
        border: solid 2px var(--color-4);
        color: var(--color-4);
        padding: 1em 1.4em;
        margin: 2em .2em;
        border-radius: .4em;
    }

    section div:nth-of-type(2) div:nth-of-type(1) button:hover  {
        background: var(--color-2);
    }

    /* RECAP */
    section div:nth-of-type(3) {
        display: grid;
        grid-template-columns: repeat(12, 1fr);
        column-gap: 2em;
        margin: 0 2em 12em 2em;
    }

    section div:nth-of-type(3) h2, section div:nth-of-type(3) p {
        grid-column-start: 4;
        grid-column-end: 10;
    }

    section div:nth-of-type(3) h2 {
        margin-bottom: 1em;
    }

    @media (max-width: 800px) {
        section div:nth-of-type(3) h2, section div:nth-of-type(3) p {
            grid-column-start: 3;
            grid-column-end: 11;
        }
    }

    @media (max-width: 560px) {
        section div:nth-of-type(3) h2, section div:nth-of-type(3) p {
            grid-column-start: 1;
            grid-column-end: 13;
        }
    }
</style>