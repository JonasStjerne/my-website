<script>
import { onMount } from "svelte";
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import * as AOS from 'aos';
import '/node_modules/aos/dist/aos.css';

//Decaling a new scene object
const scene = new THREE.Scene();

//Defining camera and propeties
var camera = new THREE.PerspectiveCamera( 75, 1 / 2, 0.1, 1000);
camera.position.setZ(2);

// Geometry
const geometry = new THREE.TorusGeometry(10, 3, 16, 100);
const material = new THREE.MeshBasicMaterial( {color: 0xFF6347, wireframe: true } );
const torus = new THREE.Mesh( geometry, material );
//scene.add(torus);


//Add ambient light
const light = new THREE.AmbientLight( 0x404040, 2 ); // soft white light
scene.add( light );

//Add directional light
const directionalLight = new THREE.DirectionalLight( 0xffffff, 5 );
directionalLight.position.set(0,1,0);
directionalLight.castShadow = true;
scene.add(directionalLight);


//Colored light
// const pointLight = new THREE.PointLight(0x0000ff, 5);
// pointLight.position.set(0, 100, 250);
// scene.add(pointLight);

//Load custom object
const loader = new GLTFLoader();
loader.load( 'assets/test_model.glb', function ( gltf ) {
	const obj = gltf.scene.children[0];
	scene.add( obj );
	obj.position.setY(-1);
}, undefined, function ( error ) {
	console.error( error );
} );


onMount(() => {
	AOS.init();
	//On mount connect render to canvas element
	// const renderer = new THREE.WebGL1Renderer({
	// 	canvas: document.querySelector('#model')
	// });
	const renderer = new THREE.WebGLRenderer({ 
		antialias: true,
		alpha: true,
		canvas: document.querySelector('#model')
	 });

	 //Interactive
	 const controls = new OrbitControls( camera,  renderer.domElement);
	controls.addEventListener('mousechange', renderer);

	//Set size and pixel ratio
	renderer.setPixelRatio( window.devicePixelRatio );
	renderer.setSize( 200, 400 );
	
	 
	//Define clock from three.js
	const clock = new THREE.Clock();

	//Animation tick
	const tick = () => {
		//Gets time between animation tick, to run at same speed across different hardware
		const elapsedTime = clock.getElapsedTime();

		//Define animation relative to time between ticks
		torus.rotation.x = 0.8 *1;
		torus.rotation.y = 1 * elapsedTime;
		torus.rotation.z = 1.2 * elapsedTime;

		//render scene
		renderer.render( scene, camera );

		//Tell browser to call tick function on next animation frame
		requestAnimationFrame( tick );
	} 

	//Call function
	tick();
	
})

</script>

<main>
	<div id="header">
		<a href="https://github.com/JonasStjerne">
			<img src="assets/github.svg" alt="Github logo" height="30">
		</a>
		<a href="https://www.linkedin.com/in/jonas-stjerne-974860150/">
			<img src="assets/linkedin.svg" alt="Github logo" height="30">
		</a>
	</div>
	<div class="homeContent">
		<div class="content" data-aos="fade-up">
			<div class="introContainer">
				<h1>Hi</h1>
				<div class="nameContainer">
					<h2 class="nameEl"> I’m Jonas</h2>
				</div>
				<p>Currently studing IT at Aalborg University and working at <a href="https://openomic.dk/">Openomic</a> as a full stack developer</p>
			</div>
		</div>
		<canvas id="model"></canvas>
	</div>
</main>

<style>
	#header {
		display: flex;
		justify-content: right;
		gap: 20px;
		margin-right: 20px;
	}

	.content {
		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: column;
	}

	.introContainer {
		width: 66%;
	}

	.homeContent {
		display: flex;
		flex-direction: row;
		justify-content: center;
	}

	.nameEl {
		margin-right: 20px;
		padding: 10px;
		color: white;
	}

	.nameContainer {
		background-image: var(--gradient);
		width: fit-content;
		margin-bottom: 10px;
	}
</style>

