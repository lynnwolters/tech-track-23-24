<script>
    import { onMount, tick } from 'svelte'
    import * as d3 from 'd3'
    // import { gsap } from 'gsap/dist/gsap'
    // import { ScrollTrigger } from 'gsap/dist/ScrollTrigger'

    let stackedBarChart
    let activeDataset = 'dataset1'
    let activeButton = 'dataset1'

    const datasets = {
        dataset1: './data/effectsDataOne.json',
        dataset2: './data/effectsDataTwo.json',
        dataset3: './data/effectsDataThree.json',
        dataset4: './data/effectsDataFour.json',
    }

    onMount(async function() {
        loadData(datasets[activeDataset])
    })

    async function loadData(url) {
        const response = await fetch(url)
        const data = await response.json()
        createStackedBarChart(data)
    }

    async function updateStackedBarChart() {
        d3.select(stackedBarChart)
            .selectAll('svg')
            .transition()
            .duration(200)
            .attr('opacity', 0)
        
        await new Promise(resolve => setTimeout(resolve, 200))

        d3.select(stackedBarChart)
            .selectAll('svg')
            .remove()
        
        await loadData(datasets[activeDataset])
        
        d3.select(stackedBarChart)
            .select('svg')
            .attr('opacity', 0)
            .transition()
            .duration(500)
            .attr('opacity', 1)
    }

    function switchDataset(dataset) {
        activeDataset = dataset
        activeButton = dataset
        tick().then(() => updateStackedBarChart())
    }

    function createStackedBarChart(data) {

        const container = document.getElementById('stacked-bar-chart-container');
        const width = container.clientWidth;
        const height = container.clientHeight;
        const margin = { top: 0, right: 200, bottom: 0, left: 200 }

        const svg = d3
            .select(stackedBarChart)
            .append('svg')
            .attr('viewBox', `0 0 ${width + margin.left + margin.right} ${height + margin.top + margin.bottom}`)
            .attr('width', '100%')
            .attr('height', '100%')
            .append('g')
            .attr('transform', `translate(${margin.left},${margin.top})`)

        const categories = Object.keys(data[0]).filter((key) => key !== 'name')

        const colorScale = d3
            .scaleOrdinal()
            .domain(categories)
            .range([
                '#9C55E3',
                '#DA47FF',
                '#C19FEC',
                '#9F86E4'
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
            .padding(0.05)

        const cornerRadiusBig = 8
        const cornerRadiusSmall = 4
        const segmentSpacing = 1.5 
        const labelOffset = 8
        const labelBackground = '#FFFFFF'

        svg.selectAll('g')
            .data(stackedData)
            .enter()
            .append('g')
            .attr('fill', (d) => colorScale(d.key)) 
            .selectAll('rect')
            .data((d) => d)
            .enter()
            .append('rect')
            .attr('x', (d) => xScale(d[0]) + segmentSpacing) 
            .attr('y', (d) => yScale(d.data.name))
            .attr('width', (d) => xScale(d[1]) - xScale(d[0]) - 2 * segmentSpacing) 
            .attr('height', yScale.bandwidth())
            .attr('rx', cornerRadiusBig) 
            .attr('ry', cornerRadiusBig)
            .attr('stroke', '#383838') 
            .attr('stroke-width', 1) 

        svg.selectAll('text')
            .data(data)
            .enter()
            .append('text')
            .attr('x', -labelOffset)
            .attr('y', (d) => yScale(d.name) + yScale.bandwidth() / 2)
            .attr('dy', '0.35em')
            .attr('fill', '#FFFFFF') 
            .attr('font-family', 'PerfectDOSVGA437')
            .attr('letter-spacing', '-1px')
            .attr('font-size', '12px')
            .style('background-color', labelBackground) 
            .attr('text-anchor', 'end')
            .text((d) => d.name)
            .text(d => d.name.toUpperCase())

        const legend = svg.append('g')
            .attr('class', 'legend')
            .attr('transform', `translate(${width + 8}, 0)`)

        const legendItems = legend.selectAll('.legend-item')
            .data(categories)
            .enter().append('g')
            .attr('class', 'legend-item')
            .attr('transform', (d, i) => `translate(0, ${i * 28})`) 

        legendItems.append('rect')
            .attr('width', 24)
            .attr('height', 24)
            .style('fill', colorScale)
            .attr('rx', cornerRadiusSmall) 
            .attr('ry', cornerRadiusSmall)
            .attr('stroke', '#383838') 
            .attr('stroke-width', 1) 

        legendItems.append('text')
            .attr('x', 32)
            .attr('y', 12) 
            .attr('dy', '.35em')
            .attr('fill', '#FFFFFF') 
            .attr('font-family', 'PerfectDOSVGA437')
            .attr('letter-spacing', '-1px')
            .attr('font-size', '12px')
            .text((d) => d)
            .text(d => d.toUpperCase())
    }
</script>

<section>
    
    <div>
        <h2 class="title-normal">
            The Effects
        </h2>
    </div>
    
    <div>
        <p class="p-text-normal">The effects that victims experience from online crime</p>
        <div>
            <button on:click={() => switchDataset('dataset1')} class:active={activeButton === 'dataset1'} class="p-text-small">All</button>
            <button on:click={() => switchDataset('dataset2')} class:active={activeButton === 'dataset2'} class="p-text-small">Scams and fraud</button>
            <button on:click={() => switchDataset('dataset3')} class:active={activeButton === 'dataset3'} class="p-text-small">Hacking</button>
            <button on:click={() => switchDataset('dataset4')} class:active={activeButton === 'dataset4'} class="p-text-small">Threats and intimidation</button>
        </div>
        <div bind:this={stackedBarChart} id="stacked-bar-chart-container"></div>
    </div>
    
    <div>
        <h2 class="title-small">Recap</h2>
        <p class="p-text-normal">
            Research indicates that individuals facing online threats or intimidation experience more pronounced mental challenges than those impacted by scams/fraud or hacking. The constant exposure to mean or threatening messages online is comparable to dealing with a persistent bully, leading to heightened emotional stress, deeper depressive symptoms, and prolonged anxiety.
            <br> <br>
            In essence, the emotional impact of online threats and intimidation is more substantial, especially when contrasted with the financial consequences of falling victim to scams/fraud or hacking. It's crucial to note that while intimidation takes a toll on mental health, fraud and scams can have significant financial implications. Deception in financial matters or identity theft resulting from hacking can lead to enduring financial challenges.
            <br> <br>
            These consequences are serious, with individuals enduring their effects for an extended period. Recognizing the gravity of these online crimes will help with the importance of establishing a safer digital environment.
    </div>

</section>

<style>
    /* TITLE */
    section > div:nth-of-type(1) {
        margin: 2em 2em 2em 2em;
        height: 100vh;
        display: flex;
        justify-content: center;
        align-items: center;
        background-color: gray;
    }

    /* CHART */
    section div:nth-of-type(2) {
        display: flex;
        flex-direction: column;
        align-items: center;
        margin: 2em 2em 2em 2em;
        background-color: gray;
    }

    section div:nth-of-type(2) div:nth-of-type(1)  {
        display: flex;
    }

    section div:nth-of-type(2) div:nth-of-type(1) button  {
        background: var(--color-3);
        border: solid 1px var(--color-4);
        color: var(--color-4);
        padding: 1em 1.4em;
        margin: 2em .2em;
        border-radius: .4em;
    }

    section div:nth-of-type(2) div:nth-of-type(1) button:hover  {
        background: var(--color-2);
    }

    section div:nth-of-type(2) div:nth-of-type(1) button.active  {
        background: var(--color-2);
    }

    #stacked-bar-chart-container {
        height: 60vh;
        width: 80%;
    }

    /* RECAP */
    section div:nth-of-type(3) {
        display: grid;
        grid-template-columns: repeat(12, 1fr);
        column-gap: 2em;
        margin: 2em 2em 2em 2em;
        background-color: gray;
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