<script>
    import { onMount } from 'svelte'
    import * as d3 from 'd3'

    let stackedBarChart

    onMount(async function() {
        const response = await fetch('./data/effectsData.json')
        const data = await response.json()
        createStackedBarChart(data)
    })

    function createStackedBarChart(data) {

        const width = 600
        const height = 400

        const svg = d3
            .select(stackedBarChart)
            .append('svg')
            .attr('width', width)
            .attr('height', height)

        const categories = Object.keys(data[0]).filter((key) => key !== 'name')

        const colorScale = d3
            .scaleOrdinal()
            .domain(categories)
            .range([
                '#9C55E3',
                '#DA47FF',
                '#C19FEC'
            ])

        const stack = d3
            .stack()
            .keys(categories)
            .order(d3.stackOrderNone)
            .offset(d3.stackOffsetExpand) 

        const stackedData = stack(data)

        const xScale = d3
            .scaleLinear()
            .domain([0, 1]) 
            .range([0, width])

        const yScale = d3
            .scaleBand()
            .domain(data.map((d) => d.name))
            .range([0, height])
            .padding(0.1)

        // Hoeken voor afgeronde rechthoeken
        const cornerRadius = 8;

        svg.selectAll('g')
            .data(stackedData)
            .enter()
            .append('g')
            .attr('fill', (d) => colorScale(d.key)) 
            .selectAll('rect')
            .data((d) => d)
            .enter()
            .append('rect')
            .attr('x', (d) => xScale(d[0]))
            .attr('y', (d) => yScale(d.data.name))
            .attr('width', (d) => xScale(d[1]) - xScale(d[0]))
            .attr('height', yScale.bandwidth())
            .attr('rx', cornerRadius) // Afgeronde hoeken
            .attr('ry', cornerRadius)
            .attr('stroke', '#383838') // Border kleur
            .attr('stroke-width', 1) // Border dikte
    }
</script>

<section>
    
    <div>
        <h2 class="title-normal">The Effects</h2>
    </div>
    
    <div>
        <p class="p-text-normal">The effects that victims experience from online crime</p>
        <div>
            <button class="p-text-small">All</button>
            <button class="p-text-small">Scams and fraude</button>
            <button class="p-text-small">Hacking</button>
            <button class="p-text-small">Threats and intimidation</button>
        </div>
        <div bind:this={stackedBarChart}></div>
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