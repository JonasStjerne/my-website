<script>
import { onMount } from "svelte";
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";

//Decaling a new scene object
const scene = new THREE.Scene();

//Defining camera and propeties
var camera = new THREE.PerspectiveCamera( 75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.setZ(3);

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
	renderer.setSize( window.innerWidth, window.innerHeight );
	
	 
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
	<h1>3D view</h1>
	<canvas id="model"></canvas>
</main>

