<script>
    import { onMount, tick } from 'svelte'
    import * as d3 from 'd3'

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
            .remove();
        
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

        const width = 600
        const height = 500
        const margin = { top: 0, right: 200, bottom: 0, left: 200 }

        const svg = d3
            .select(stackedBarChart)
            .append('svg')
            .attr('width', width + margin.left + margin.right)
            .attr('height', height + margin.top + margin.bottom)
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
        <h2 class="title-normal">The Effects</h2>
    </div>
    
    <div>
        <p class="p-text-normal">The effects that victims experience from online crime</p>
        <div>
            <button on:click={() => switchDataset('dataset1')} class:active={activeButton === 'dataset1'} class="p-text-small">All</button>
            <button on:click={() => switchDataset('dataset2')} class:active={activeButton === 'dataset2'} class="p-text-small">Scams and fraud</button>
            <button on:click={() => switchDataset('dataset3')} class:active={activeButton === 'dataset3'} class="p-text-small">Hacking</button>
            <button on:click={() => switchDataset('dataset4')} class:active={activeButton === 'dataset4'} class="p-text-small">Threats and intimidation</button>
        </div>
        <div bind:this={stackedBarChart}></div>
    </div>
    
    <div>
        <h2 class="title-small">Recap</h2>
        <p class="p-text-normal">
            The research findings highlight that individuals who encounter online intimidation or threats undergo more significant mental challenges compared to those affected by fraud/scams or hacking. Confronting online threats can result in heightened emotional stress, leading to deeper depressive symptoms and prolonged anxiety issues.
            <br> <br>
            To illustrate, picture this: dealing with constant mean or threatening messages online is akin to facing an unrelenting bully who never seems to disappear. This ongoing situation can evoke intense feelings of sadness and anxiety, and these emotions might linger for an extended period.
            <br> <br>
            In straightforward terms, the impact of online intimidation is comparable to shouldering a heavier emotional burden, especially when contrasted with the consequences of falling victim to scams or experiencing hacking. It's crucial to understand that while intimidation and threats take a more substantial toll on mental health, fraud and scams can have significant financial implications. Being deceived in financial matters or experiencing identity theft due to hacking can lead to lasting financial challenges.
            <br> <br>
            These consequences are serious, and individuals may endure the effects for an extended period. Recognizing the gravity of these online crimes underscores the importance of establishing a safer digital environment for everyone, where both mental and financial well-being are prioritized and protected.</p>
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

    section div:nth-of-type(2) div:nth-of-type(1) button.active  {
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