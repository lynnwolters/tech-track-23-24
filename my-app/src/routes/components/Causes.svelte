<script>
    import { onMount, tick } from 'svelte'
    import * as d3 from 'd3'
    import { gsap } from 'gsap/dist/gsap'
    import { ScrollTrigger } from 'gsap/dist/ScrollTrigger'

    let radarChart
    let activeDataset = 'dataset1'
    let additionalText = "I don't think it's necessary"
    let activeButton = 'dataset1'

    const datasets = {
        dataset1: './data/causesDataOne.json',
        dataset2: './data/causesDataTwo.json',
        dataset3: './data/causesDataThree.json',
    }

    onMount(async function() {
        loadData(datasets[activeDataset])
    })

    async function loadData(url) {
        const response = await fetch(url)
        const data = await response.json()
        createRadarChart(data)

        gsap.registerPlugin(ScrollTrigger)

        ScrollTrigger.create({
            trigger: '.type-effect-causes-trigger',
            start: 'top top',
            end: '+=100%',
            // pin: true,
            onEnter: () => typeEffectCauses(),
        })
    }

    function typeEffectCauses() {
        const text = document.querySelector('.type-effect-causes')
        const characters = text.textContent.split('')

        text.textContent = ''

        gsap.to(text, { opacity: 1 })

        characters.forEach((char, index) => {
            const span = document.createElement('span')
            span.textContent = char
            span.style.opacity = 0
            text.appendChild(span)

            gsap.to(span, {
                opacity: 1,
                duration: 0,
                delay: index * 0.075,
                onComplete: () => {
                    if (index === characters.length - 1) {
                        const cursor = document.createElement('span')
                        cursor.textContent = '|'
                        cursor.classList.add('cursor')
                        text.appendChild(cursor)

                        gsap.to(cursor, { opacity: 0, repeat: -1, yoyo: true, duration: 0.7 })
                    }
                }
            })
        })
    }

    async function updateRadarChart() {
        d3.select(radarChart)
            .selectAll('svg')
            .transition()
            .duration(200)
            .attr('opacity', 0)
        
        await new Promise(resolve => setTimeout(resolve, 200))

        d3.select(radarChart)
            .selectAll('svg')
            .remove();
        
        await loadData(datasets[activeDataset])
        
        d3.select(radarChart)
            .select('svg')
            .attr('opacity', 0)
            .transition()
            .duration(500)
            .attr('opacity', 1)
    }

    function switchDataset(dataset) {
        activeDataset = dataset
        activeButton = dataset
        switch (dataset) {
            case 'dataset1':
                additionalText = "“I don't think it's necessary.”";
                break;
            case 'dataset2':
                additionalText = "“I find it too difficult.”";
                break;
            case 'dataset3':
                additionalText = "“Takes too much time to do it.”";
                break;
        }
        tick().then(() => updateRadarChart())
    }

    function createRadarChart(data) {
        const container = document.getElementById('radar-chart-container');
        const width = container.clientWidth;
        const height = container.clientHeight;
        const margin = { top: 150, right: 200, bottom: 150, left: 150 }

        const maxValue = Math.max(...data.map(d => d.value))

        const angleSlice = (Math.PI * 2) / data.length

        const svg = d3.select(radarChart)
            .append('svg')
            .attr('viewBox', `0 0 ${width + margin.left + margin.right} ${height + margin.top + margin.bottom}`)
            .attr('width', '100%')
            .attr('height', '100%')
            .append('g')
            .attr('transform', `translate(${(width + margin.left + margin.right) / 2}, ${(height + margin.top + margin.bottom) / 2})`)

        const radius = d3.scaleLinear()
            .domain([0, maxValue])
            .range([0, Math.min(width / 2, height / 2)])

        const line = d3.lineRadial()
            .angle((d, i) => i * angleSlice)
            .radius(d => radius(d.value))
            .curve(d3.curveCardinalClosed.tension(0.5))

        svg.append('path')
            .datum(data)
            .attr('class', 'radar-chart-area')
            .attr('d', line)
            .attr('fill', 'rgba(255, 255, 255, 0.1)')

        svg.append('path')
            .datum(data)
            .attr('class', 'radar-chart-line')
            .attr('d', line)
            .attr('stroke', '#FFFFFF')
            .attr('fill', 'none')
            .attr('stroke-width', 1)

        const circleRadius = [0.35, 0.7, 1.05]

        circleRadius.forEach(r => {
            svg.append('circle')
                .attr('r', radius(maxValue) * r)
                .attr('stroke', 'rgba(255, 255, 255, 0.6)')
                .attr('fill', 'none')
                .attr('stroke-dasharray', '4')
        })

        const labelRadiusMultiplier = 1.4 
        const labelWidthLimit = 120

        const labels = svg.selectAll('.radar-chart-label')
            .data(data)
            .enter().append('text')
            .attr('fill', '#FFFFFF') 
            .attr('font-family', 'PerfectDOSVGA437')
            .attr('letter-spacing', '-1px')
            .attr('font-size', '12px')
            .attr('class', 'radar-chart-label')
            .attr('x', (d, i) => labelRadiusMultiplier * radius(maxValue) * Math.cos(angleSlice * i - Math.PI / 2))
            .attr('y', (d, i) => labelRadiusMultiplier * radius(maxValue) * Math.sin(angleSlice * i - Math.PI / 2))
            .text(d => d.name)
            .text(d => d.name.toUpperCase())
            .attr('text-anchor', 'middle')
            .call(wrap, labelWidthLimit)

        const lines = svg.selectAll('.radar-chart-line-connect')
            .data(data)
            .enter().append('line')
            .attr('class', 'radar-chart-line-connect')
            .attr('x1', 0)
            .attr('y1', 0)
            .attr('x2', (d, i) => labelRadiusMultiplier * radius(maxValue) * Math.cos(angleSlice * i - Math.PI / 2))
            .attr('y2', (d, i) => labelRadiusMultiplier * radius(maxValue) * Math.sin(angleSlice * i - Math.PI / 2))
            .attr('stroke', 'rgba(255, 255, 255, 0.6)')
            .attr('stroke-width', 1)
            .attr('stroke-dasharray', '4')

        function wrap(text, width) {
            text.each(function () {
                var text = d3.select(this),
                    words = text.text().split(/\s+/).reverse(),
                    word,
                    line = [],
                    lineNumber = 0,
                    lineHeight = 1.1,
                    y = text.attr("y"),
                    x = text.attr("x"),
                    dy = parseFloat(text.attr("dy")) || 0,
                    tspan = text.text(null).append("tspan").attr("x", x).attr("y", y).attr("dy", dy + "em")
                while (word = words.pop()) {
                    line.push(word)
                    tspan.text(line.join(" "))
                    if (tspan.node().getComputedTextLength() > width) {
                        line.pop()
                        tspan.text(line.join(" "))
                        line = [word]
                        tspan = text.append("tspan").attr("x", x).attr("y", y).attr("dy", ++lineNumber * lineHeight + dy + "em").text(word)
                    }
                }
            })
        }
    }
</script>

<section class="type-effect-causes-trigger">
    
    <div>
        <h2 class="title-normal type-effect-causes">
            The Cause
        </h2>
    </div>
    
    <div>
        <p class="p-text-normal">Reasons why people didn’t take security measurements</p>
        <div>
            <button on:click={() => switchDataset('dataset1')} class:active={activeButton === 'dataset1'} class="p-text-small">Reason 1</button>
            <button on:click={() => switchDataset('dataset2')} class:active={activeButton === 'dataset2'} class="p-text-small">Reason 2</button>
            <button on:click={() => switchDataset('dataset3')} class:active={activeButton === 'dataset3'} class="p-text-small">Reason 3</button>
        </div> 
        <p class="p-text-normal">{additionalText}</p>
        <div bind:this={radarChart} id="radar-chart-container"></div> 
    </div>
    
    <div>
        <h2 class="title-small">Recap</h2>
        <p class="p-text-normal">
            In the world of online security, some folks find it tricky to put effective safety measures in place. The fast-changing online scene makes it hard to keep up with the latest protection methods. For some, the technical side of online security is a bit overwhelming, and that can make them hesitant to take action.
            <br> <br>
            Neglecting security also adds to the growing number of online crime victims. Some people don't take security seriously or believe they won't be targeted, leading them to skip protective steps.
            <br> <br>
            Plus, there's a chunk of folks who become victims by not realizing how risky their online activities can be. They might not fully grasp the potential dangers, leaving them open to various online crimes.
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
    }

    .type-effect-causes {
        opacity: 0;
    }

    /* CHART */
    section div:nth-of-type(2) {
        display: flex;
        flex-direction: column;
        align-items: center;
        margin: 2em 2em 2em 2em;
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

    #radar-chart-container {
        height: 60vh;
        width: 80%;
    }

    /* RECAP */
    section div:nth-of-type(3) {
        display: grid;
        grid-template-columns: repeat(12, 1fr);
        column-gap: 2em;
        margin: 12em 2em 12em 2em;
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