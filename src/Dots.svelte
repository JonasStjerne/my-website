<script>
    import { onMount } from "svelte";
    import * as d3 from "../node_modules/d3";

    onMount(() => {

        const nodes = [
            {"id": "Alice"},
            {"id": "Bob"},
            {"id": "Carol"}
        ];

        const links = [
        {"source": 0, "target": 1}, // Alice → Bob
        {"source": 1, "target": 2} // Bob → Carol
        ];

        const chartHeight        = 800,
                chartWidth         = 900,
                nodeHeight         = 11,
                nodeWidth          = 16
        const svg = d3.select("#tech-skills")
        svg.attr("height", chartHeight)
            .attr("width", chartWidth)

        // functions to avoid nodes and links going outside the svg container, when calculating the position:
        function getNodeXCoordinate(x) {
            return Math.max(0, Math.min(chartWidth - nodeWidth, x))
        }
        function getNodeYCoordinate(y) {
            return Math.max(0, Math.min(chartHeight - nodeHeight, y))
        }

        // create a new simulation (a simulation starts with alpha = 1 and decrese it slowly to 0):
        const simulation = d3.forceSimulation()
                        // many-body force (force applied amongst all nodes, negative strength for repulsion):
                        .force("charge", d3.forceManyBody().strength(-40).distanceMax(150))
                        // centering force (mean position of all nodes):
                        .force("center", d3.forceCenter(chartWidth / 2, chartHeight / 2)) 
                        // link force (pushes linked nodes together or apart according to the desired link distance):
                        .force("link", d3.forceLink())
                        // prevent nodes from ovelapping, treating them as circles with the given radius:
                        .force("collide", d3.forceCollide((nodeWidth + 2) / 2))
        

        const url = 'https://raw.githubusercontent.com/DealPete/forceDirected/master/countries.json'

        const nodeElements = svg.append('g')
        .selectAll('circle')
        .data(nodes)
        .enter().append('circle')
            .attr('r', 10)
            .attr('fill', "gray")
        
        const textElements = svg.append('g')
            .selectAll('text')
            .data(nodes)
            .enter().append('text')
                .text(node => node.label)
                .attr('font-size', 15)
                .attr('dx', 15)
                .attr('dy', 4)
        
        simulation.nodes(nodes).on("tick", () => {
            nodeElements
                .attr("cx", node => node.x)
                .attr("cy", node => node.y)
            textElements
                .attr("x", node => node.x)
                .attr("y", node => node.y)
        })

        simulation.force('link', d3.forceLink()
            .id(link => link.id)
            .strength(1))

            const linkElements = svg.append('g')
                .selectAll('line')
                .data(links)
                .enter().append('line')
                    .attr('stroke-width', 1)
                    .attr('stroke', '#E5E5E5')
            
            linkElements
                .attr('x1', link => link.source.x)
                .attr('y1', link => link.source.y)
                .attr('x2', link => link.target.x)
                .attr('y2', link => link.target.y)
    });

</script>

<div id='mainContainer'>
    <svg id="tech-skills"></svg>
</div>

<style>
   #mainContainer {
	text-align: center;
}

.links {
	stroke: red;
}

.node {
	fill: transparent;
	stroke: black;
	stroke-width: 3px;
}


</style>