<script>
import { onMount } from "svelte";
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import * as AOS from 'aos';
import "../node_modules/aos/dist/aos.css";
import Dots from './Dots.svelte';
import 'animate.css';


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


	//Animation hover effect
	const elements = document.querySelectorAll('span');

	for (let i = 0; i < elements.length; i++) {
		elements[i].addEventListener('animationend', function(e) {
			elements[i].classList.remove('animate__rubberBand');
		});

		elements[i].addEventListener('mouseover', function(e) {
			elements[i].classList.add('animate__rubberBand')
		})
	}

})

</script>

<main>
	<div id="header">
		<a class="social-link social-link--github" id="github" href="https://github.com/JonasStjerne">
			<svg class="social-svg" viewBox="0 0 600 600" xmlns="http://www.w3.org/2000/svg">
			  <title>
				github
			  </title>
			  <g class="social-group" fill="none" fill-rule="evenodd">
				<circle class="social-group__outline" stroke="#000" stroke-width="20" cx="300" cy="300" r="262.5" />
				<circle class="social-group__inner-circle" fill="#000" cx="300" cy="300" r="252.5" />
				<path class="social-group__icon" d="M300 150c-82.8348 0-150 68.8393-150 153.817 0 67.9687 42.991 125.558 102.5893 145.9151 7.5 1.4063 10.2455-3.3482 10.2455-7.433 0-3.683-.134-13.3259-.2009-26.183-41.7187 9.308-50.558-20.625-50.558-20.625-6.8304-17.7456-16.6741-22.5-16.6741-22.5-13.5938-9.576 1.0044-9.375 1.0044-9.375 15.067 1.0714 22.9688 15.8705 22.9688 15.8705 13.3929 23.5045 35.0893 16.741 43.6607 12.7902 1.3393-9.9107 5.2232-16.741 9.509-20.558-33.2813-3.884-68.3036-17.076-68.3036-76.0045 0-16.808 5.8259-30.5357 15.4018-41.25-1.5402-3.884-6.6965-19.5536 1.4732-40.7143 0 0 12.5893-4.1518 41.25 15.7366 11.9866-3.4152 24.7768-5.0893 37.567-5.1562 12.7231.067 25.5803 1.741 37.5669 5.1562 28.6607-19.8884 41.183-15.7366 41.183-15.7366 8.1697 21.1607 3.0134 36.8304 1.4733 40.7143 9.5758 10.7812 15.4017 24.509 15.4017 41.25 0 59.0625-35.0892 72.0536-68.5044 75.8705 5.3571 4.7545 10.1785 14.1295 10.1785 28.4598 0 20.558-.2009 37.1652-.2009 42.1875 0 4.0849 2.6786 8.9063 10.3125 7.3661C407.076 429.308 450 371.7187 450 303.817 450 218.8393 382.8348 150 300 150z"
				  fill="#FFF" fill-rule="nonzero" />
			  </g>
			</svg>
		</a>
		<a class="social-link social-link--linkedin" id="linkedin" href="https://www.linkedin.com/in/jonas-stjerne-974860150/">
			<svg class="social-svg" viewBox="0 0 600 600" xmlns="http://www.w3.org/2000/svg">
			  <title>
				linkedin
			  </title>
			  <g class="social-group" fill="none" fill-rule="evenodd">
				<circle class="social-group__outline" stroke="#000" stroke-width="20" cx="300" cy="300" r="262.5" />
				<circle class="social-group__inner-circle" fill="#2D76B0" cx="300" cy="300" r="252.5" />
				<path class="social-group__icon" d="M278.9308 253.1923h43.5769v20.0539h.5923c6.0923-11.5077 20.9-23.6077 43.0692-23.6077 46.0308 0 54.577 30.2923 54.577 69.723v80.2154h-45.4385v-71.1615c0-17.0077-.2539-38.8385-23.6077-38.8385-23.6923 0-27.2462 18.5308-27.2462 37.5693v72.4307h-45.4384l-.0846-146.3846zm-74.1231 0h45.523V399.577h-45.523V253.1923zm22.8461-72.7692c14.5539 0 26.4 11.8461 26.4 26.4 0 14.5538-11.8461 26.4-26.4 26.4-14.6384 0-26.4-11.8462-26.4-26.4 0-14.5539 11.7616-26.4 26.4-26.4z"
				  fill="#000" fill-rule="nonzero" />
			  </g>
			</svg>
		  </a>
		<!-- <a href="https://github.com/JonasStjerne">
			<img src="assets/github.svg" alt="Github logo" height="30">
		</a>
		<a href="https://www.linkedin.com/in/jonas-stjerne-974860150/">
			<img src="assets/linkedin.svg" alt="Github logo" height="30">
		</a> -->
	</div>
	<div class="homeContent">
		<div class="content">
			<div class="introContainer">
				<h1 data-aos="fade-right" data-aos-duration="800"><span>H</span><span>i</span></h1>
				<div data-aos="fade-right" data-aos-delay="100" data-aos-duration="800" class="nameContainer">
					<h2 class="nameEl">
						{#each "I'm Jonas" as char}
							{#if char == " "}
								&#160
							{:else}
								<span>{char}</span>
							{/if}
						{/each}
					</h2>
				</div>
				<p class="underText" data-aos="fade-right" data-aos-delay="200" data-aos-duration="800">
					{#each "Currently studying IT at Aalborg University and working at" as char}
						{#if char == " "}
							&#160
						{:else}
							<span>{char}</span>
						{/if}
					{/each}
					<a href="https://openomic.dk/">
						Openomic
					</a> 
					{#each "as a full stack developer" as char}
						{#if char == " "}
							&#160
						{:else}
							<span>{char}</span>
						{/if}
					{/each}
				</p>
			</div>
		</div>
		<canvas id="model"></canvas>
	</div>
	<Dots/>
</main>

<style>

	span {
		height:fit-content;
		animation-duration: 1s;
		display: inline-block;
	}

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

	.underText {
		width: fit-content;
	}

	.social-link--linkedin {
	color: #2d76b0;
	}
	.social-link--github {
	color: #000;
	}

	.social-link .social-svg {
	width: 3rem;
	height: 3rem;
	}
	.social-link .social-svg .social-group__icon {
	fill: #191308;
	transition: all 0.2s;
	}
	.social-link .social-svg .social-group__inner-circle {
	fill: transparent;
	transition: all 0.2s;
	}
	.social-link .social-svg .social-group__outline {
	stroke: #191308;
	transform-origin: 50% 50%;
	transition: all 0.2s;
	}
	.social-link .social-svg:hover .social-group__icon, .social-link .social-svg:active .social-group__icon, .social-link .social-svg:focus .social-group__icon {
	fill: #FFFBFA;
	transition: all 0.45s;
	}
	.social-link .social-svg:hover .social-group__inner-circle, .social-link .social-svg:active .social-group__inner-circle, .social-link .social-svg:focus .social-group__inner-circle {
	fill: currentColor;
	transition: all 0.45s;
	}

	.social-link .social-svg:hover .social-group__outline, .social-link .social-svg:active .social-group__outline, .social-link .social-svg:focus .social-group__outline {
	stroke: currentColor;
	transform: scale(1.1);
	transition: all 0.45s;
	}
</style>

