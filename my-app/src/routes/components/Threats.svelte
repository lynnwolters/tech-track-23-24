<script>
    import { onMount } from "svelte";
    import * as d3 from "d3";
  
    onMount(async () => {
      fetch('./data/data.json')
        .then(response => response.json())
        .then(data => {
          console.log(data);
  
          // Convert flat data to hierarchical structure
          const hierarchicalData = d3.stratify()
            .id(d => d["Details onderwerp"])
            .parentId(d => d["Onderwerp"])
            (data);
  
          // Breedte, hoogte en straal van het diagram
          const width = 500;
          const height = 500;
          const radius = Math.min(width, height) / 2;
  
          // Kleurenschaal
          const color = d3.scaleOrdinal(d3.schemeCategory10);
  
          // Maak een SVG-element
          const svg = d3.select("#chart-container").append("svg")
            .attr("width", width)
            .attr("height", height)
            .append("g")
            .attr("transform", "translate(" + width / 2 + "," + height / 2 + ")");
  
          // Creëer een partition lay-out en wijs de data toe
          const partition = d3.partition()
            .size([2 * Math.PI, radius]);
  
          const root = partition(hierarchicalData);
  
          // Creëer boogpaden en voeg deze toe aan het diagram
          const arc = d3.arc()
            .startAngle(d => d.x0)
            .endAngle(d => d.x1)
            .innerRadius(d => d.y0)
            .outerRadius(d => d.y1);
  
          svg.selectAll("path")
            .data(root.descendants())
            .enter().append("path")
            .attr("d", arc)
            .style("fill", d => color(d.data.id)); // Use id for color
  
        })
        .catch(err => {
          console.error("Error fetching data:", err);
        });
    });
  </script>
  

<section>
    
    <div>
        <h2 class="title-normal">The Threats</h2>
    </div>
    
    <div>
        <div>
            <p class="p-text-normal">Total online crime in 2022 <br> (32.861 respondents)</p>
            <div id="chart-container"></div>
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
        margin: 0 2em 12em 2em
    }

    section > div:nth-of-type(1) h2 {
        text-align: center
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