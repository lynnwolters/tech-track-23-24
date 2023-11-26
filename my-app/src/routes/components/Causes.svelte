<script>
    import { onMount } from 'svelte'
    import * as d3 from 'd3'

    let radarChart

    onMount(async function() {
        const response = await fetch('./data/causesData.json')
        const data = await response.json()
        createRadarChart(data)
    })

    function createRadarChart(data) {
        const width = 400
        const height = 400
        const margin = { top: 150, right: 200, bottom: 150, left: 200 }

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
                .attr('stroke', '#FFFFFF')
                .attr('fill', 'none')
                .attr('stroke-dasharray', '4')
        })

        const labelRadiusMultiplier = 1.4 
        const labelWidthLimit = 120; // Je kunt dit aanpassen aan je behoeften

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
            .call(wrap, labelWidthLimit);

        const lines = svg.selectAll('.radar-chart-line-connect')
            .data(data)
            .enter().append('line')
            .attr('class', 'radar-chart-line-connect')
            .attr('x1', 0)
            .attr('y1', 0)
            .attr('x2', (d, i) => labelRadiusMultiplier * radius(maxValue) * Math.cos(angleSlice * i - Math.PI / 2))
            .attr('y2', (d, i) => labelRadiusMultiplier * radius(maxValue) * Math.sin(angleSlice * i - Math.PI / 2))
            .attr('stroke', '#FFFFFF')
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
                    tspan = text.text(null).append("tspan").attr("x", x).attr("y", y).attr("dy", dy + "em");
                while (word = words.pop()) {
                    line.push(word);
                    tspan.text(line.join(" "));
                    if (tspan.node().getComputedTextLength() > width) {
                        line.pop();
                        tspan.text(line.join(" "));
                        line = [word];
                        tspan = text.append("tspan").attr("x", x).attr("y", y).attr("dy", ++lineNumber * lineHeight + dy + "em").text(word);
                    }
                }
            });
        }
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
        <p class="p-text-normal">“I don't think it's necessary.”</p>
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

    section div:nth-of-type(2) div:nth-of-type(1) button:nth-of-type(2)  {
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