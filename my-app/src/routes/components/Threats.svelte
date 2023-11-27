<script>
    import { onMount } from 'svelte'
    import {gsap}  from "gsap/dist/gsap"       
    import {ScrollTrigger} from "gsap/dist/ScrollTrigger"
    import * as d3 from 'd3'

    let sunburstChart

    onMount(async function() {
        const response = await fetch('./data/threatsData.json')
        const data = await response.json()
        const generatedSunburstChart = createSunburstChart(data)
        d3.select(sunburstChart).append(() => generatedSunburstChart)
    })

    function createSunburstChart(data) {
        const width = 600
        const height = 600

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
            .attr('width', width)
            .attr('height', height)

        const cell = svg
            .selectAll('g')
            .data(root.descendants())
            .join('g')

        const colorScale = d3.scaleOrdinal()
            .domain(['scams and fraud', 'purchase fraud', 'payment fraud', 'identity fraud', 'phishing', 'hacking', 'device', 'account', 'threats and intimidation', 'shame texting', 'stalking', 'bullying', 'threats'])
            .range(['#9C55E3', '#9C55E3', '#9C55E3', '#9C55E3', '#9C55E3', '#DA47FF', '#DA47FF', '#DA47FF', '#C19FEC', '#C19FEC', '#C19FEC', '#C19FEC', '#C19FEC'])

        cell.append('path')
            .attr('d', arc)
            .attr('fill', d => {
                return d.depth > 0 ? colorScale(d.data.name) : 'none'
            })
            .attr('stroke', d => {
                return d.depth > 0 ? '#383838' : 'none'
            })
            .attr('stroke-width', d => {
                return d.depth > 0 ? 1 : 0
        })

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
        .call(selection => addTextToCell(selection, getTransformFunction))

    cell.filter(d => d.depth === 2)
        .call(selection => addTextToCell(selection, getTransformFunction))

    return svg.node()
    }
</script>

<section>
    <div>
        <h2 class="title-normal">The Threats</h2>
    </div>
    <div>
        <div>
            <p class="p-text-normal">Total online crime in 2022 <br> (32.861 respondents)</p>
            <div bind:this={sunburstChart}></div>
        </div>
        <div>
            <p class="p-text-normal">A significant chunk of online crime involves fraud and scams. In particular, financial fraud plays a major role, with shady individuals using tricky methods to take money from people who aren't aware of their schemes.</p>
        </div>
    </div>
    <div>
        <h2 class="title-small">Recap</h2>
        <p class="p-text-normal">
            Scams / fraud: Online scams often involve fake websites, emails, or messages that seem legitimate but aim to trick you into providing personal or financial information. It's like a digital con game where the bad actors pretend to be someone trustworthy to steal your money or sensitive data.
            <br> <br>
            Hacking:
            Hacking, which means breaking into computer systems without permission, is a growing problem. This isn't just about invading someone's privacy—it can lead to identity theft and losing money.
            <br> <br>
            Imagine someone breaking into your virtual "house" and going through your personal stuff. That's what hackers do in the digital world. They might steal your passwords, get access to your bank accounts, or even take control of your social media accounts.
            <br> <br>
            Intimidation / threats:
            When people use the internet to scare or threaten others, it can have serious effects on their mental well-being. Imagine feeling constantly worried or scared because someone is sending you mean or threatening messages online.
            <br> <br>
            This form of online crime can lead to deep feelings of sadness and anxiety. It's like dealing with a bully, but instead of facing them in person, the intimidation happens through screens and keyboards.
            <br> <br>
            Understanding these categories is crucial because it helps us recognize the tactics bad actors use online and empowers us to protect ourselves better. By being aware of these dangers, we can navigate the digital world more safely.</p>
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
    section > div:nth-of-type(2) {
        display: grid;
        grid-template-columns: repeat(12, 1fr);
        column-gap: 2em;
        margin: 0 2em 12em 2em;
    }

    section > div:nth-of-type(2) div:nth-of-type(1) {
        grid-column-start: 2;
        grid-column-end: 8;
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    section > div:nth-of-type(2) div:nth-of-type(1) p {
        margin-bottom: 2em;
        text-align: center;
    }

    section > div:nth-of-type(2) div:nth-of-type(2) {
        grid-column-start: 9;
        grid-column-end: 12;
        display: flex;
        align-items: center;
    }

    /* RECAP */
    section > div:nth-of-type(3) {
        display: grid;
        grid-template-columns: repeat(12, 1fr);
        column-gap: 2em;
        margin: 0 2em 12em 2em;
    }

    section > div:nth-of-type(3) h2, section div:nth-of-type(3) p {
        grid-column-start: 4;
        grid-column-end: 10;
    }

    section > div:nth-of-type(3) h2 {
        margin-bottom: 1em;
    }

    @media (max-width: 960px) {
        section > div:nth-of-type(2) div:nth-of-type(1) {
            grid-column-start: 2;
            grid-column-end: 12;
            margin-bottom: 12em;
        }

        section > div:nth-of-type(2) div:nth-of-type(2) {
            grid-column-start: 4;
            grid-column-end: 10;
        }
    }

    @media (max-width: 800px) {
        section > div:nth-of-type(2) div:nth-of-type(1) {
            grid-column-start: 1;
            grid-column-end: 13;
        }

        section > div:nth-of-type(2) div:nth-of-type(2) {
            grid-column-start: 3;
            grid-column-end: 11;
        }

        section > div:nth-of-type(3) h2, section div:nth-of-type(3) p {
            grid-column-start: 3;
            grid-column-end: 11;
        }
    }

    @media (max-width: 560px) {
        section > div:nth-of-type(2) div:nth-of-type(1) {
            grid-column-start: 1;
            grid-column-end: 13;
        }

        section > div:nth-of-type(2) div:nth-of-type(2) {
            grid-column-start: 1;
            grid-column-end: 13;
        }

        section > div:nth-of-type(3) h2, section div:nth-of-type(3) p {
            grid-column-start: 1;
            grid-column-end: 13;
        }
    }
</style>