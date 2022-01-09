<script>
    import { onMount } from "svelte";
    import * as d3 from "../node_modules/d3";

    onMount(() => {

        // const nodes = [
        //     {"id": "Alice"},
        //     {"id": "Bob"},
        //     {"id": "Carol"}
        // ];

        // const links = [
        // {"source": 0, "target": 1}, // Alice → Bob
        // {"source": 1, "target": 2} // Bob → Carol
        // ];

        // const chartHeight        = 800,
        //         chartWidth         = 900,
        //         nodeHeight         = 11,
        //         nodeWidth          = 16
        // const svg = d3.select("#tech-skills")
        // svg.attr("height", chartHeight)
        //     .attr("width", chartWidth)

        // // functions to avoid nodes and links going outside the svg container, when calculating the position:
        // function getNodeXCoordinate(x) {
        //     return Math.max(0, Math.min(chartWidth - nodeWidth, x))
        // }
        // function getNodeYCoordinate(y) {
        //     return Math.max(0, Math.min(chartHeight - nodeHeight, y))
        // }

        // // create a new simulation (a simulation starts with alpha = 1 and decrese it slowly to 0):
        // const simulation = d3.forceSimulation()
        //                 // many-body force (force applied amongst all nodes, negative strength for repulsion):
        //                 .force("charge", d3.forceManyBody().strength(-40).distanceMax(150))
        //                 // centering force (mean position of all nodes):
        //                 .force("center", d3.forceCenter(chartWidth / 2, chartHeight / 2)) 
        //                 // link force (pushes linked nodes together or apart according to the desired link distance):
        //                 .force("link", d3.forceLink())
        //                 // prevent nodes from ovelapping, treating them as circles with the given radius:
        //                 .force("collide", d3.forceCollide((nodeWidth + 2) / 2))
        //                 .force('link').link(links);
        

        // const url = 'https://raw.githubusercontent.com/DealPete/forceDirected/master/countries.json'

        // const nodeElements = svg.append('g')
        // .selectAll('circle')
        // .data(nodes)
        // .enter().append('circle')
        //     .attr('r', 10)
        //     .attr('fill', "red")
        
        // const textElements = svg.append('g')
        //     .selectAll('text')
        //     .data(nodes)
        //     .enter().append('text')
        //         .text(node => node.label)
        //         .attr('font-size', 15)
        //         .attr('dx', 15)
        //         .attr('dy', 4)
        
        // simulation.nodes(nodes).on("tick", () => {
        //     nodeElements
        //         .attr("cx", node => node.x)
        //         .attr("cy", node => node.y)
        //     textElements
        //         .attr("x", node => node.x)
        //         .attr("y", node => node.y)
        // })

        // simulation.force('link', d3.forceLink()
        //     .id(link => link.id)
        //     .strength(1))

        //     const linkElements = svg.append('g')
        //         .selectAll('line')
        //         .data(links)
        //         .enter().append('line')
        //             .attr('stroke-width', 1)
        //             .attr('stroke', '#E5E5E5')
            
        //     linkElements
        //         .attr('x1', link => link.source.x)
        //         .attr('y1', link => link.source.y)
        //         .attr('x2', link => link.target.x)
        //         .attr('y2', link => link.target.y)




        const chartHeight        = 800,
		  chartWidth         = 900,
		  nodeHeight         = 11,
		  nodeWidth          = 16

const svg = d3.select("#svgchart")
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

const url = 'assets/countries.json'
d3.json(url).then( function(data) {

	const nodes = data.nodes
	const links = data.links

	// add links (lines):
	const link = svg.append("g")
					      .attr("class", "links")
					    .selectAll("line")
					    .data(links)
					    .enter().append("line") // line to connect nodes
					      .attr("stroke-width", 1) // line width

    // use a g element to contain a rect and an image for every node:
    const nodeWrapper = svg.append("g")
                                            .attr("class", "nodes")
                                            .selectAll(".node")
                                            .data(nodes)
                                            .enter().append("g")
                                            .attr("class", "nodeWrapper")

        // add nodes (rects):
        const node = nodeWrapper
                                .append("rect")
                                .attr("id", d => d.code)
                                .attr("class", "node")
                                .attr("width", nodeWidth)
                                .attr("height", nodeHeight)
    
    // add flag images:
    nodeWrapper
        .append("image")
            .attr("href", d => "https://github.com/fabvit86/d3-force-directed-graph/raw/master/public/images/flag-" + d.code + ".png")
            .attr("height", nodeHeight)
            .attr("width", nodeWidth)

    // node dragging:
    nodeWrapper
        .call(d3.drag()
            .on("start", d => {
                // heat the simulation:
                if (!d3.event.active) simulation.alphaTarget(0.2).restart()
                // set fixed x and y coordinates:	
                d.fx = d.x
                d.fy = d.y
                console.log("Started");
            })
            .on("drag", d => {
                console.log("drag updated");
                console.log(d3.event);
                console.log(d.x);
                // by fixing its position, this disables the forces acting on the node:
                d.fx = d3.event.x
                d.fy = d3.event.y
            })
            .on("end", d => {
                console.log("drag ended");
                // stop simulation:
                if (!d3.event.active) simulation.alphaTarget(0)
                // reactivate the force on the node:
                d.fx = null
                d.fy = null
            })
        )

    // tooltip div:
    const tooltip = d3.select('#mainContainer').append("div")
                                        .classed("tooltip", true)
                                        .style("opacity", 0) // start invisible
    nodeWrapper
        .on("mouseover", function(d) {
            tooltip.transition()
                .duration(300)
                .style("opacity", 1) // show the tooltip
            tooltip.html(d.country)
            .style("left", (d3.event.pageX - d3.select('.tooltip').node().offsetWidth - 5) + "px")
            .style("top", (d3.event.pageY - d3.select('.tooltip').node().offsetHeight) + "px");
        })
        .on("mouseleave", function(d) {
            tooltip.transition()
                .duration(200)
                .style("opacity", 0)
        })

    simulation
        .nodes(nodes)
        .on("tick", () => {
            // set each node's position on each tick of the simulation:
            nodeWrapper.attr("transform", d => "translate(" + getNodeXCoordinate(d.x) + "," + getNodeYCoordinate(d.y) + ")")
            // set start (x1,y1) and point (x2,y2) coordinate of each link on each tick of the simulation:
            link.attr("x1", d => getNodeXCoordinate(d.source.x + nodeWidth / 2))
            link.attr("y1", d => getNodeYCoordinate(d.source.y + nodeHeight / 2))
            link.attr("x2", d => getNodeXCoordinate(d.target.x + nodeWidth / 2))
            link.attr("y2", d => getNodeYCoordinate(d.target.y + nodeHeight / 2))
        })

        // pass the links to the link force:
        simulation
            .force("link")
            .links(links)
                .distance(45)
    })










});

</script>

<div id='mainContainer'>
    <svg id="svgchart"></svg>
</div>

<style>
   #mainContainer {
	text-align: center;
}

    #svgchart {
        background-color: white;
    }



</style>