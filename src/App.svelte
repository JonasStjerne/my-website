<script>
import { onMount } from "svelte";
import * as THREE from 'three';



	onMount(() => {
		//Decaling a new scene object
		const scene = new THREE.Scene();

		//Defining camera and propeties
		const camera = new THREE.PerspectiveCamera( 75, window.innerWidth / window.innerHeight, 0.1, 1000);

		const renderer = new THREE.WebGL1Renderer({
			canvas: document.querySelector('#model')
		});

		renderer.setPixelRatio( window.devicePixelRatio );
		renderer.setSize( window.innerWidth, window.innerHeight );
		camera.position.setZ(30);

		renderer.render( scene, camera );

		const geometry = new THREE.TorusGeometry(10, 3, 16, 100);
		const material = new THREE.MeshBasicMaterial( {color: 0xFF6347, wireframe: true } );
		const torus = new THREE.Mesh( geometry, material );

		scene.add(torus);

		function animate() {
			requestAnimationFrame( animate );
			renderer.render( scene, camera );
		}

		animate();
		
	})

</script>

<main>
	<h1>3D view</h1>
	<canvas id="model"></canvas>
</main>

