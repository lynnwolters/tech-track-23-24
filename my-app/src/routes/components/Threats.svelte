<script>
	import {onMount} from 'svelte'
	import * as d3 from 'd3'

	let sunburstChart
	
	onMount(async function() {
        const response = await fetch('./data/data.json')
		const data = await response.json()
		console.log(data)
		const chart = Sunburst(data, {
			value: d => d.value, 
			label: d => d.name, 
			title: (d, n) => `${n.ancestors().reverse().map(d => d.data.name).join(".")}\n${n.value.toLocaleString("en")}`, 
			width: 600,
			height: 600
		})
		d3.select(sunburstChart).append(() => chart)
	})
	
	function Sunburst(data, { 
		path, 
		id = Array.isArray(data) ? d => d.id : null, 
		parentId = Array.isArray(data) ? d => d.parentId : null, 
		children, 
		value, 
		sort = (a, b) => d3.descending(a.value, b.value), 
		label, 
		title, 
		link, 
		linkTarget = "_blank", 
		width = 640, 
		height = 400, 
		margin = 1, 
		marginTop = margin, 
		marginRight = margin, 
		marginBottom = margin, 
		marginLeft = margin, 
		padding = 4, 
		radius = Math.min(width - marginLeft - marginRight, height - marginTop - marginBottom) / 2, 
		color = d3.interpolateRainbow, 
		fill = "#ccc", 
		fillOpacity = 0.6, 
	} = {}) {

		const root = path != null ? d3.stratify().path(path)(data)
				: id != null || parentId != null ? d3.stratify().id(id).parentId(parentId)(data)
				: d3.hierarchy(data, children)
		
		value == null ? root.count() : root.sum(d => Math.max(0, value(d)))

		if (sort != null) root.sort(sort)

		d3.partition().size([2 * Math.PI, radius])(root)

		if (color != null) {
			color = d3.scaleSequential([0, root.children.length - 1], color).unknown(fill)
			root.children.forEach((child, i) => child.index = i)
		}

		const arc = d3.arc()
				.startAngle(d => d.x0)
				.endAngle(d => d.x1)
				.padAngle(d => Math.min((d.x1 - d.x0) / 2, 2 * padding / radius))
				.padRadius(radius / 2)
				.innerRadius(d => d.y0)
				.outerRadius(d => d.y1 - padding)

		const svg = d3.create("svg")
				.attr("viewBox", [
					marginRight - marginLeft - width / 2,
					marginBottom - marginTop - height / 2,
					width,
					height
				])
				.attr("width", width)
				.attr("height", height)
				.attr("style", "max-width: 100% height: auto height: intrinsic")
				.attr("font-family", "sans-serif")
				.attr("font-size", 10)
				.attr("text-anchor", "middle")

		const cell = svg
			.selectAll("a")
			.data(root.descendants())
			.join("a")
				.attr("xlink:href", link == null ? null : d => link(d.data, d))
				.attr("target", link == null ? null : linkTarget)

		cell.append("path")
				.attr("d", arc)
				.attr("fill", color ? d => color(d.ancestors().reverse()[1]?.index) : fill)
				.attr("fill-opacity", fillOpacity)

		if (label != null) cell
			.filter(d => (d.y0 + d.y1) / 2 * (d.x1 - d.x0) > 10)
			.append("text")
				.attr("transform", d => {
					if (!d.depth) return
					const x = (d.x0 + d.x1) / 2 * 180 / Math.PI
					const y = (d.y0 + d.y1) / 2
					return `rotate(${x - 90}) translate(${y},0) rotate(${x < 180 ? 0 : 180})`
				})
				.attr("dy", "0.32em")
				.text(d => label(d.data, d))

		if (title != null) cell.append("title")
				.text(d => title(d.data, d))

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
            <p class="p-text-normal">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam.</p>
        </div>
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