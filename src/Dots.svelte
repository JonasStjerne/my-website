<script>
  import { onMount } from "svelte";

  let link, node, text, h, w, simulation;
   const data = {
  "nodes": [
    {"text": "Angular", "r": "40", "types": "Framework"},
    {"text": "Svelte","r": "40", "types": "Framework"},
    {"text": "Vue","r": "40", "types": "Framework"},
    {"text": "JavaScript", "r": "50", "types": "Language"},
    {"text": "TypeScript", "r": "45", "types": "Language"},
    {"text": "Node.js", "r": "35", "types": "Language"},
    {"text": "C#", "r": "50", "types": "Language"},
    {"text": "PHP", "r": "40", "types": "Language"},
    {"text": "MySQL","r": "30", "types": "Database"},
    {"text": "RPA","r": "30", "types": "RPA"},
    {"text": "Git","r": "25", "types": "Source Control"},
    {"text": "AdobeXD","r": "40", "types": "Design"},
    {"text": "Figma","r": "35", "types": "Design"},
    {"text": ".Net Core","r": "40", "types": ".Net"},
    {"text": "Blazor","r": "35", "types": ".Net"},
  ],
  "links": [
    { "target": 1, "source": 0 },
    { "target": 2, "source": 1 },
    { "target": 2, "source": 0 },
    { "target": 4, "source": 3 },
    { "target": 5, "source": 3 },
    { "target": 6, "source": 3 },
    { "target": 13, "source": 6 },
    { "target": 14, "source": 6 },
    { "target": 3, "source": 7 },
    { "target": 6, "source": 7 },
    { "target": 12, "source": 11 },
    { "target": 13, "source": 14 },
    { "target": 1, "source": 3 },
  ]
}

const clamp = (val, min, max) => Math.min(Math.max(val, min), max);



const types = [
"Framework",
"Language",
"Markup",
"Source Control",
"RPA",
"Database",
"Design",
".Net"
];

//Colors
const color = d3.scaleSequential(d3.interpolateRainbow)
    .domain([0,types.length]);

// define constants
const { nodes, links } = data;



const dragstarted = (d) => {
  if (!d3.event.active) simulation.alphaTarget(0.3).restart();
  d.fx = d.x;
  d.fy = d.y;
}

const dragged = (d) => {
  d.fx = clamp(d3.event.x, 0, w);
  d.fy = clamp(d3.event.y, 0, h);
}

const dragended = (d) => {
  if (!d3.event.active) simulation.alphaTarget(0);
  d.fx = null;
  d.fy = null;
}

function getNodeXCoordinate(x , r) {
    return Math.max(r, Math.min(w - r, x));
}
function getNodeYCoordinate(y, r) {
    return Math.max(r, Math.min(h - r, y));
}
onMount(() => {
  w = document.body.clientWidth;
  h = 500;

  // initialize simulation
  simulation = d3.forceSimulation()
    // keep entire simulation balanced around screen center
  //   .force('center', d3.forceCenter(w/2, h/2))
    // pull toward center
    .force('attract', d3.forceAttract()
      .target([w/2, h/2])
      .strength(0.11))
    // cluster by region
    .force('cluster', d3.forceCluster()
      .centers(d => types.indexOf(d.types))
      .strength(1)
      .centerInertia(0.1))

    .force("link", d3.forceLink())
    .force('charge', d3.forceManyBody().strength(-10))
    // apply collision with padding
    .force("collide", d3.forceCollide().radius(d => d.r).strength(0));


    // ramp up collision strength to provide smooth transition
  const transitionTime = 3000;
  const t = d3.timer((elapsed) => {
    const dt = elapsed / transitionTime;
    simulation.force('collide').strength(Math.pow(dt, 2) * 0.7);
    if (dt >= 1.0) t.stop();
  });

  //initialize svg
  const svg = d3.select("#svgchart")
    .attr('width', w )
    .attr('height', h )

    // initialize links
  link = svg.append("g")
    .attr("class", "link")
    .selectAll("line")
    .data(links)
    .enter()
    .append("line");

  // initialize node circles
  node = svg
    .append("g")
    .attr("class", "node")
    .selectAll("circle")
    .data(nodes)
    .enter().append("circle")
      .attr("r", d => d.r)
      .attr("fill", d => color(types.indexOf(d.types)))
      .attr("stroke", d => color(types.indexOf(d.types)))
      .call(d3.drag()
          .on("start", dragstarted)
          .on("drag", dragged)
          .on("end", dragended));

  text = svg
    .append("g")
    .attr("class", "textBoxes noselect")
    .selectAll("text")
    .data(nodes)
    .enter()
    .append("text")
    .attr('text-anchor', "middle")
    .text(d => d.text)
    .attr('color', 'black')
    .attr('font-size', 15)
    .call(d3.drag()
      .on("start", dragstarted)
      .on("drag", dragged)
      .on("end", dragended));

  simulation
    .nodes(nodes)
    .on("tick", ticked);

  simulation.force("link")
      .links(links);
})



 // define tick function
const ticked = () => {
  link
      .attr("x1", d => d.source.x)
      .attr("y1", d => d.source.y)
      .attr("x2", d => d.target.x)
      .attr("y2", d => d.target.y);

  node
    .attr("cx", d => getNodeXCoordinate(d.x, d.r) + "px")
    .attr("cy", d => getNodeYCoordinate(d.y, d.r) + "px");
  
  text
    .attr("x", d => getNodeXCoordinate(d.x, d.r) + "px")
    .attr("y", d => getNodeYCoordinate(d.y, d.r) + 4 +"px");

}



</script>

<div id='mainContainer'>
    <svg id="svgchart"></svg>
</div>

<style>
   #mainContainer {
	text-align: center;
}




</style>