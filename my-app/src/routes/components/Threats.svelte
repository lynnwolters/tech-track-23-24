<script>
    import { onMount } from 'svelte'
    import * as d3 from 'd3'

    let sunburstChart

    onMount(async function() {
        const response = await fetch('./data/threatsData.json')
        const data = await response.json()
        const generatedSunburstChart = createSunburstChart(data)
        d3.select(sunburstChart).append(() => generatedSunburstChart)
    })

    function createSunburstChart(data) {
        const container = document.getElementById('sunburst-diagram-container');
        const width = container.clientWidth;
        const height = container.clientHeight;

        const root = d3.hierarchy(data, d => d.children)
        root.sum(d => Math.max(0, d.value))

        const partition = d3.partition()
            .size([2 * Math.PI, Math.min(width, height) / 2])
            .padding(0.01)
        partition(root)

        const arc = d3.arc()
            .startAngle(d => d.x0)
            .endAngle(d => d.x1)
            .innerRadius(d => d.y0)
            .outerRadius(d => Math.max(0, d.y1 - 3))
            .cornerRadius(8)

        const svg = d3.create('svg')
            .attr('viewBox', [-width / 2, -height / 2, width, height])
            .attr('width', '100%')
            .attr('height', '100%')

        const cell = svg
            .selectAll('g')
            .data(root.descendants())
            .join('g')

        const colorScale = d3.scaleOrdinal()
            .domain(['scams and fraud', 'purchase fraud', 'payment fraud', 'identity fraud', 'phishing', 'hacking', 'device', 'account', 'threats and intimidation', 'shame texting', 'stalking', 'bullying', 'threats'])
            .range(['#9C55E3', '#9C55E3', '#9C55E3', '#9C55E3', '#9C55E3', '#DA47FF', '#DA47FF', '#DA47FF', '#C19FEC', '#C19FEC', '#C19FEC', '#C19FEC', '#C19FEC'])

        cell.append('path')
            .attr('d', arc)
            .attr('fill', d => d.depth > 0 ? colorScale(d.data.name) : 'none')
            .attr('stroke', d => d.depth > 0 ? '#383838' : 'none')
            .attr('stroke-width', d => d.depth > 0 ? 1 : 0)

        function addTextToCell(selection, transformFunction) {
            selection
                .append('text')
                .attr('transform', transformFunction)
                .attr('text-anchor', 'middle')
                .attr('font-size', '12px')
                .attr('fill', '#383838')
                .attr('font-family', 'PerfectDOSVGA437')
                .attr('letter-spacing', '-1px')
                .each(function (d) {
                    const text = d.data.name.toUpperCase()
                    const words = text.split(' ')

                    for (let i = 0; i < words.length; i++) {
                        const tspan = d3.select(this)
                            .append('tspan')
                            .attr('dy', i === 0 ? 0 : '1em')
                            .attr('x', 0)
                            .attr('text-anchor', 'middle')
                            .text(words[i])
                    }
                })
        }

        function getTransformFunction(d) {
            if (d.depth === 1) {
                const [x, y] = arc.centroid(d)
                const angle = (d.x0 + (d.x1 - d.x0) / 2)
                let rotation = angle * (180 / Math.PI)
                if (rotation >= 90 && rotation <= 270) {
                    rotation += 180
                }
                return `translate(${x}, ${y}) rotate(${rotation})`
            } else if (d.depth === 2) {
                const x = (d.x0 + d.x1) / 2 * 180 / Math.PI
                const y = (d.y0 + d.y1) / 2
                return `rotate(${x - 90}) translate(${y},0) rotate(${x < 180 ? 0 : 180})`
            }
        }

        cell.filter(d => d.depth === 1)
            .attr('class', 'circle-1')
            .call(selection => addTextToCell(selection, getTransformFunction))

        cell.filter(d => d.depth === 2)
            .attr('class', 'circle-2')
            .call(selection => addTextToCell(selection, getTransformFunction))

        return svg.node()
    }
</script>

<section>
    <div>
        <h2 class="title-normal">
            The Threats
        </h2>
    </div>
    <div>
        <div>
            <p class="p-text-normal">Total online crime in 2022 <br> (32.861 respondents)</p>
            <div bind:this={sunburstChart} id="sunburst-diagram-container"></div>
        </div>
        <div>
            <p class="p-text-normal">A significant chunk of online crime involves fraud and scams. In particular, financial fraud plays a major role.</p>
        </div>
    </div>
    <div>
        <h2 class="title-small">Recap</h2>
        <p class="p-text-normal">
            Online scams and fraud involve deceptive practices conducted through websites, emails, or messages. The goal is to trick individuals into disclosing personal or financial information. It's essentially a digital deception where perpetrators create a facade of trustworthiness to steal sensitive data or money.
            <br> <br>
            Hacking refers to the unauthorized entry into computer systems or networks. This act compromises privacy and can lead to identity theft, financial loss, or the unauthorized control of digital assets. 
            <br> <br>
            Threats and intimidation in the online realm involve actions that can severely impact individuals' mental well-being. This includes constant worry or fear induced by mean or threatening messages. It's an online form of bullying that uses digital platforms to create a hostile environment, causing distress, sadness, and anxiety.
            <br> <br>
            Understanding these categories is crucial for recognizing online threats and empowering individuals to navigate the digital world safely. Awareness helps combat tactics used by malicious actors.</p>
    </div>
</section>

<style>
    /* TITLE */
    section > div:nth-of-type(1){
        margin: 2em 2em 2em 2em;
        height: 100vh;
        display: flex;
        justify-content: center;
        align-items: center;
        background-color: gray;
    }

    /* CHART */
    section div:nth-of-type(2) {
        display: grid;
        align-items: center;
        grid-template-columns: repeat(12, 1fr);
        column-gap: 2em;
        margin: 2em 2em 2em 2em;
        background-color: gray;
    }

    section div:nth-of-type(2) div:nth-of-type(1) {
        grid-column-start: 2;
        grid-column-end: 8;
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    #sunburst-diagram-container {
        height: 60vh;
        width: 100%;
    }

    section div:nth-of-type(2) div:nth-of-type(1) p {
        margin-bottom: 2em;
        text-align: center;
    }

    section div:nth-of-type(2) div:nth-of-type(2) {
        grid-column-start: 8;
        grid-column-end: 12;
        display: flex;
        align-items: center;
    }

    /* RECAP */
    section > div:nth-of-type(3) {
        display: grid;
        grid-template-columns: repeat(12, 1fr);
        column-gap: 2em;
        margin: 2em 2em 2em 2em;
        background-color: gray;
    }

    section > div:nth-of-type(3) h2, section div:nth-of-type(3) p {
        grid-column-start: 4;
        grid-column-end: 10;
    }

    section > div:nth-of-type(3) h2 {
        margin-bottom: 1em;
    }

    @media (max-width: 960px) {
        section div:nth-of-type(2) div:nth-of-type(1) {
            grid-column-start: 2;
            grid-column-end: 12;
        }

        section div:nth-of-type(2) div:nth-of-type(2) {
            display: none;
        }
    }

    @media (max-width: 800px) {
        section div:nth-of-type(2) div:nth-of-type(1) {
            grid-column-start: 1;
            grid-column-end: 13;
        }

        section div:nth-of-type(2) div:nth-of-type(2) {
            display: none;
        }

        section > div:nth-of-type(3) h2, section div:nth-of-type(3) p {
            grid-column-start: 3;
            grid-column-end: 11;
        }
    }

    @media (max-width: 560px) {
        section div:nth-of-type(2) div:nth-of-type(1) {
            grid-column-start: 1;
            grid-column-end: 13;
        }

        section div:nth-of-type(2) div:nth-of-type(2) {
            display: none;
        }

        section > div:nth-of-type(3) h2, section div:nth-of-type(3) p {
            grid-column-start: 1;
            grid-column-end: 13;
        }
    }
</style>