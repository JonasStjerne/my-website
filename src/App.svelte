<script>
import { onMount } from "svelte";
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import * as AOS from 'aos';
import "../node_modules/aos/dist/aos.css";
import Dots from './Dots.svelte';

let originY, originX;

//Define clock from three.js
const clock = new THREE.Clock();

let model, skeleton, renderer, mixer, animations;
//Decaling a new scene object
const scene = new THREE.Scene();

//Defining camera and propeties
var camera = new THREE.PerspectiveCamera( 75, 1 / 2, 0.1, 1000);
camera.position.setZ(2);
camera.position.setY(0);

//Add ambient light
const light = new THREE.AmbientLight( 0x404040, 2 ); // soft white light
scene.add( light );

//Add directional light
const directionalLight = new THREE.DirectionalLight( 0xffffff, 2 );
directionalLight.position.set(0,1,2);
directionalLight.castShadow = true;
scene.add(directionalLight);

//Animation tick
const tick = () => {

	//Tell browser to call tick function on next animation frame
	requestAnimationFrame( tick );

	//Animation update, with the delta time of last update
	if (mixer ) mixer.update( clock.getDelta() );

	//render scene
	renderer.render( scene, camera );

	
} 

function calcAngle(opposite, adjacent) {
  return Math.atan(opposite / adjacent) * 180/Math.PI;
}

//Get rotation of head
function RotateHead(x,y) {
	let degRatio = 3/180; //3 rotation on model = 180 degrees
	let m = 1000; //Depth distance to the model. The lower the more head movement

	// //Simple version of head calc. Not as good a result
	skeleton.bones[5].rotation.y = -0.6+(x/originX)*0.6;
	skeleton.bones[5].rotation.x = -0.05+(y/originY)*0.05;

	
	// //Calculates the estimated angle of head rotation according to curser movement
	// if (y > originY) {
	// 	skeleton.bones[5].rotation.x = 
	// 	calcAngle(window.innerHeight-originY, m)*
	// 	degRatio*
	// 	((y-originY)/(window.innerHeight-originY));
	// } else {
	// 	skeleton.bones[5].rotation.x = 
	// 	-calcAngle(originY, m)*
	// 	degRatio*
	// 	((originY-y)/originY);
	// }

	// if (x > originX) {
	// 	skeleton.bones[5].rotation.y = 
	// 	calcAngle(window.innerWidth-originX, m)*
	// 	degRatio*
	// 	((x-originX)/(window.innerWidth-originX));
	// } else {
	// 	skeleton.bones[5].rotation.y = 
	// 	-calcAngle(originX, m)*
	// 	degRatio*
	// 	((originX-x)/originX);
	// }
}


onMount(() => {
	AOS.init();

	renderer = new THREE.WebGLRenderer({ 
		antialias: true,
		alpha: true,
		canvas: document.querySelector('#model')
	 });

	 //Get postition of model
	 let element = document.querySelector('#model');
			let rect = element.getBoundingClientRect();
			originX = (rect.right - rect.left)/2 + rect.left; //Center position of model
			originY =  rect.top + 50;

	//  //Interactive
	//  const controls = new OrbitControls( camera,  renderer.domElement);
	// controls.addEventListener('mousechange', renderer);

	//Set size and pixel ratio
	renderer.setPixelRatio( window.devicePixelRatio );
	renderer.setSize( 300, 600 );

	//Load custom object
	const loader = new GLTFLoader();
	loader.load( 'assets/Xbot.glb', function ( gltf ) {
		console.log("Load2");
		model = gltf.scene;
		//Add to scene
		scene.add( model );
		model.position.setY(-1);
		//Setup skelton
		skeleton = new THREE.SkeletonHelper(model);
		skeleton.visible = false;
		scene.add( skeleton );

		//Setup animations
		mixer = new THREE.AnimationMixer( model );
		//Start hello animation
		mixer.clipAction(gltf.animations[3]).play(); //Change this to hello animation

		
		//Call tick function to start animation
		tick();

		//Add eventlistiner for mouse move and call function to move model head after hello-animation has run
		setTimeout(() => {
			mixer.clipAction(gltf.animations[3]).fadeOut(1);
			 document.addEventListener("mousemove", e => RotateHead(e.pageX, e.pageY));
		}, 2000);

	}, undefined, function ( error ) {
		console.error( error );
	} );
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
		<div class="content">
			<div class="introContainer">
				<h1 data-aos="fade-right" data-aos-duration="800">Hi</h1>
				<div data-aos="fade-right" data-aos-delay="100" data-aos-duration="800" class="nameContainer">
					<h2 class="nameEl"> I’m Jonas</h2>
				</div>
				<p data-aos="fade-right" data-aos-delay="200" data-aos-duration="800">Currently studing IT at Aalborg University and working at <a href="https://openomic.dk/">Openomic</a> as a full stack developer</p>
			</div>
		</div>
		<canvas id="model"></canvas>
	</div>
	<Dots/>
</main>

<style>
	#header {
		display: flex;
		justify-content: right;
		gap: 20px;
		margin-right: 20px;
		margin-top: 10px;
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

