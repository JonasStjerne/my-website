<script>
  import { onMount } from "svelte";
  import Gradient from "javascript-color-gradient";
  const colorGradient = new Gradient();
  
  let link, node, text, h, w, simulation, grade;
   const data = {
  "nodes": [
    {"id" : 0, "text": "Angular", "r": "55", "types": "Framework", "initialX" : "345.31596164743587", "initialY" : "189.15349152740902"},
    {"id" : 1, "text": "Vue","r": "55", "types": "Framework", "initialX" : "355.45314764282796", "initialY" : "268.4997049720481"},
    {"id" : 3, "text": "JavaScript", "r": "50", "types": "Language", "initialX" : "270.43616060491706", "initialY" : "239.08718406127946"},
    {"id" : 4, "text": "C#", "r": "40", "types": "Language", "initialX" : "298.3934782289567", "initialY" : "324.5876401317703"},
    {"id" : 5, "text": "PHP", "r": "40", "types": "Language", "initialX" : "219.26882601831906", "initialY" : "313.0955897542832"},
    {"id" : 6, "text": "MySQL","r": "30", "types": "Database", "initialX" : "407.35532205082353", "initialY" : "221.56284590239827"},
    {"id" : 7, "text": "Git","r": "25", "types": "Source Control", "initialX" : "307.8295090235146", "initialY" : "388.89348650755284"},
    {"id" : 8, "text": "Figma","r": "35", "types": "Design", "initialX" : "212.45022640388", "initialY" : "176.95100030647427"},
    {"id" : 9, "text": ".Net Core","r": "40", "types": ".Net", "initialX" : "375.42842288639594", "initialY" : "1345.9764636192515"},
    {"id" : 10, "text": "Blazor","r": "35", "types": ".Net", "initialX" : "426.8418927251777", "initialY" : "291.401994824919"},
    {"id" : 11, "text": "Node.js","r": "35", "types": "RPA", "initialX" : "278.7683508949712", "initialY" : "154.55739227994252"},
    // {"id" : 12, "text": "React","r": "55", "types": "Framework", "initialX" : "355.45314764282796", "initialY" : "268.4997049720481"},
    // {"id" : 13, "text": "React","r": "40", "types": "Framework", "initialX" : "355.45314764282796", "initialY" : "268.4997049720481"},
    // {"id" : 14, "text": "Svelte","r": "40", "types": "Framework", "initialX" : "355.45314764282796", "initialY" : "268.4997049720481"},
    // {"id" : 15, "text": "TypeScript","r": "40", "types": "Framework", "initialX" : "355.45314764282796", "initialY" : "268.4997049720481"},
    // {"id" : 16, "text": "CSS & HTML","r": "45", "types": "Framework", "initialX" : "355.45314764282796", "initialY" : "268.4997049720481"},
  ],
  "links": [
    { "target": 1, "source": 0 },
    { "target": 2, "source": 1 },
    { "target": 2, "source": 0 },
    { "target": 4, "source": 3 },
    { "target": 3, "source": 2 },
    { "target": 4, "source": 2 },
    { "target": 3, "source": 8 },
    { "target": 8, "source": 9 },
    { "target": 2, "source": 10 }
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
  w = document.querySelector("#skillsWrapper").clientWidth;
  console.log(w);
  h = document.querySelector("#skillsWrapper").clientHeight;

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


    //ramp up collision strength to provide smooth transition
  const transitionTime = 2500;
  const t = d3.timer((elapsed) => {
    const dt = elapsed / transitionTime;
    simulation.force('collide').strength(Math.pow(dt, 2) * 0.7);
    if (dt >= 1.0) t.stop();
  });

  //initialize svg
  const svg = d3.select("#svgchart");


  // initialize node circles
  node = svg
    .append("g")
    .attr("class", "node")
    .selectAll("circle")
    .data(nodes)
    .enter().append("circle")
      .attr("r", d => d.r)
      .attr("fill", d => "url(#gradient" + d.id +")")
      .each(function(d) { d.x = w/2; d.y = h/2})
      .call(d3.drag()
          .on("start", dragstarted)
          .on("drag", dragged)
          .on("end", dragended));

//Initialize text
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

  //Set gradient colors
  const color1 = "#FF9900";
  const color2 = '#FF8713';
  const color3 = '#FF198D';
  const color4 = "#FF00A8";
  colorGradient.setGradient(color1, color2, color3, color4);
  // colorGradient.setGradient(color1, color4);
  

  //Make a linearGradient for each node
  grade = svg
    .append("defs")
    .selectAll("linearGradient")
    .data(nodes)
    .enter();

    grade
    .append("linearGradient")
    .attr("id", d => "gradient" + d.id)
    .append("stop")
      .attr("stop-color", "rgba(255, 153, 0, 1)")
      .attr("offset", "0%")
      .attr("class", "startGrade")
    
    svg.selectAll("linearGradient")
    .append("stop")
      .attr("stop-color", "rgba(255, 0, 168, 1)")
      .attr("offset", "100%")
      .attr("class", "endGrade")


  simulation
    .nodes(nodes)
    .on("tick", ticked);

  simulation.force("link")
      .links(links);
})



 // define tick function
const ticked = () => {

  node
    .attr("cx", d => getNodeXCoordinate(d.x, d.r) + "px")
    .attr("cy", d => getNodeYCoordinate(d.y, d.r) + "px");
  
  text
    .attr("x", d => getNodeXCoordinate(d.x, d.r) + "px")
    .attr("y", d => getNodeYCoordinate(d.y, d.r) + 4 +"px");


//Update node color according to pos
  grade
  .selectAll(".startGrade")
    .attr("stop-color", d => colorGradient.getColor( Math.max(0.1, ((d.x-d.r)/w)*10 )) );
    
  grade
  .selectAll(".endGrade")
    .attr("stop-color", d => colorGradient.getColor( Math.max(0.1, ((d.x+d.r)/w)*10 )));
    

}



</script>
  <svg id="svgchart"></svg>
<style>

  #svgchart {
    width: 100%;
    height: 500px;
  }

</style>