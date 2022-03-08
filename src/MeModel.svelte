<script>
    import { onMount } from "svelte";
    import * as THREE from 'three';
    import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
    import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
    import { createEventDispatcher } from 'svelte';

    let originY, originX, w, h, camera;
    
    //Define clock from three.js
    const clock = new THREE.Clock();
    
    let model, skeleton, renderer, mixer, animations;
    //Decaling a new scene object
    const scene = new THREE.Scene();
    
    //Load Manager
   const manager = new THREE.LoadingManager();
    
   //Define dispatch
   const dispatch = createEventDispatcher();

    manager.onLoad = function () {
        dispatch("loaded", "meModel" )
    }


    //Add ambient light
    const light = new THREE.AmbientLight( 0x404040, 6); // soft white light
    scene.add( light );
    
    //Add directional light
    // const directionalLight = new THREE.DirectionalLight( 0xffffff, 1 );
    // directionalLight.position.set(0, 30,10);
    // directionalLight.castShadow = true;
    // scene.add(directionalLight);
    
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
    
        // //Simple version of head calc
        // skeleton.bones[5].rotation.y = -0.6+(x/originX)*0.6;
        // skeleton.bones[5].rotation.x = -0.05+(y/originY)*0.05;
    
        
        // //Calculates the estimated angle of head rotation according to curser movement
        if (y > originY) {
        	skeleton.bones[5].rotation.x = 
        	calcAngle(window.innerHeight-originY, m)*
        	degRatio*
        	((y-originY)/(window.innerHeight-originY));
        } else {
        	skeleton.bones[5].rotation.x = 
        	-calcAngle(originY, m)*
        	degRatio*
        	((originY-y)/originY);
        }
    
        if (x > originX) {
        	skeleton.bones[5].rotation.y = 
        	calcAngle(window.innerWidth-originX, m)*
        	degRatio*
        	((x-originX)/(window.innerWidth-originX));
        } else {
        	skeleton.bones[5].rotation.y = 
        	-calcAngle(originX, m)*
        	degRatio*
        	((originX-x)/originX);
        }
    }
    
    
    onMount(() => {
        
        renderer = new THREE.WebGLRenderer({ 
            antialias: true,
            alpha: true,
            canvas: document.querySelector('#model')
         });
    
         //Get postition of model
         let element = document.querySelector('#model');
                let rect = element.getBoundingClientRect();
                originX = rect.right - (rect.right - rect.left)/2; //Center position of model
                originY =  rect.top + 50;
    
        //  //Interactive
        //  const controls = new OrbitControls( camera,  renderer.domElement);
        // controls.addEventListener('mousechange', renderer);
    
        //Set size and pixel ratio
        w = 700;
        h = 400;
        renderer.setPixelRatio( window.devicePixelRatio );
        renderer.setSize( w, h );
        //Defining camera and propeties
        camera = new THREE.PerspectiveCamera( 75, w/h, 1, 1000);
        camera.position.setZ(5);
        camera.position.setY(4);
        camera.zoom = 2;
        camera.updateProjectionMatrix();



        //Load custom object
        const loader = new GLTFLoader(manager);
        loader.load( 'assets/model.glb', function ( gltf ) {
            
            model = gltf.scene;
            //Add to scene
            scene.add( model );
            model.position.setY(-1.5);
            //Setup skelton
            skeleton = new THREE.SkeletonHelper(model);
            skeleton.visible = false;
            scene.add( skeleton );
    
            //Setup animations
            mixer = new THREE.AnimationMixer( model );
            //Start hello animation
            setTimeout(() => {
                mixer.clipAction(gltf.animations[0]).play();
            }, 1000);
            
            //Magic to get three.js to render the model correctly.
            gltf.scene.traverse((child) => {
                if ( child.type == 'SkinnedMesh' ) {
                    child.frustumCulled = false;
                }
                if ( child.material ) child.material.metalness = 0;
            });
            
            //Call tick function to start animation
            tick();
    
            //Add eventlistiner for mouse move and call function to move model head after hello-animation has run
            setTimeout(() => {
                mixer.clipAction(gltf.animations[0]).fadeOut(1);
                 document.addEventListener("mousemove", e => RotateHead(e.pageX, e.pageY));
            }, 4000);
    
        }, undefined, function ( error ) {
            console.error( error );
        } );
    
    
    })
    
    </script>
    <div class="position-relative">
        <div class="maskWindow">
            <canvas id="model" style="display:block;" class="mx-auto"></canvas>
        </div>
        <div class="modelWindow" style="width: {h/Math.sqrt(2)-50}px; height: {h/Math.sqrt(2)-50}px ;"></div>
    </div>

   <style>
       .modelWindow {
           background-image: var(--gradient);
           position: absolute;
           left: 50%;
           transform: translateX(-50%) rotate(45deg);
           z-index: -1;
           border-radius: 5px;
           bottom: 50px;
       }

       .maskWindow {
        -webkit-mask-image: url("/assets/modelMask.png");
        -webkit-mask-repeat: no-repeat;
        -webkit-mask-position: bottom;

        mask-image: url("assets/modelMask.png");
        mask-repeat: no-repeat; 
        mask-position: bottom;
       }


   </style>

    