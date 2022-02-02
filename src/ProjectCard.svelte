<script>
    import * as AOS from 'aos';
    import "../node_modules/aos/dist/aos.css";
    import { onMount } from 'svelte';
    AOS.init();
    
    const projects = [
        {
            "title" : "Chat app",
            "text" : "Small chat app made with Socket.io to get familiar with it",
            "tech" : "Socket.io & JavaScript",
            "srcGithub" : "https://github.com/",
            "src" : "assets/projectImages/Chatter.png",
            "colorsHex" : ["00C9FF", "00FF8B"]
        },
        {
            "title" : "Chat app2",
            "text" : "Small chat app made with Socket.io to get familiar with it",
            "tech" : "Socket.io & JavaScript",
            "srcGithub" : "https://github.com/",
            "src" : "assets/projectImages/Chatter.png",
            "colorsHex" : ["FF9900", "FF00A8"]
        },
        {
            "title" : "Chat app3",
            "text" : "Small chat app made with Socket.io to get familiar with it",
            "tech" : "Socket.io & JavaScript",
            "srcGithub" : "https://github.com/",
            "src" : "assets/projectImages/Chatter.png",
            "colorsHex" : ["00FFE0", "BD00FF"]
        }
    ]; 
    let selectedProject = 0;
    let currentProjectImage, projectImageContainer;
    $ : percentProjectBar = ( (selectedProject + 1) / projects.length ) * 100;

    function removeAfterAnimation(elm) {
        elm.addEventListener('animationend', (e) => {
                elm.remove()
                console.log("removed");
                console.log(elm);            
		}, { once: true });
    }

    function slideImage(side, inOrOut, elm) {
        //remove existing animation classes
        elm.classList.remove('c_slideInRight');
        elm.classList.remove('c_slideInLeft');

        if (side =="left") {
            switch (inOrOut) {
                case "in":
                    elm.classList.add("c_slideInLeft")
                    currentProjectImage = elm;
                break;
                case "out":
                    removeAfterAnimation(elm);
                    elm.classList.add("c_slideOutLeft")
                break;
            }
        } else {
            switch (inOrOut) {
                case "in":
                    elm.classList.add("c_slideInRight")
                    currentProjectImage = elm;
                break;
                case "out":
                    removeAfterAnimation(elm);
                    elm.classList.add("c_slideOutRight")
                break;
            }
        }
    }
    

    function createNewImageElement() {
        let imageElement = document.createElement("img");
        imageElement.classList.add("h-50", "position-absolute", "top-50", "shadowDarker", "p-0", "projectImage");
        imageElement.style.cssText = 'transform: translateY(-50%); border-radius: 8px;';
        return imageElement;
    }

    function nextProject() {

        if (selectedProject < projects.length - 1) {
            //Increment to new project index
            selectedProject++;

            //Slide out current image
           slideImage("left", "out", currentProjectImage);

            //Create new image for the next project
            let newImageElement = createNewImageElement();
            newImageElement.src = projects[selectedProject].src;

            //Add the new image to the dom. The animation will automatically trigger
            projectImageContainer.appendChild(newImageElement);
            slideImage("right", "in", newImageElement)
          
        }
    }

    function prevProject() {
        if (selectedProject > 0) {
            //Increment to new project index
            selectedProject--;

            //Slide out current image
           slideImage("right", "out", currentProjectImage);

            //Create new image for the next project
            let newImageElement = createNewImageElement();
            newImageElement.src = projects[selectedProject].src;

            //Add the new image to the dom. The animation will automatically trigger
            projectImageContainer.appendChild(newImageElement);
            slideImage("left", "in", newImageElement)
          
        }
    }

    onMount(() => {
        currentProjectImage = document.querySelector('.projectImage');
    })

</script>

<div class="my-5 d-flex justify-content-center">
    <div class="position-relative container m-0 col-8" style="height:0;width:30%;padding-bottom:30%;">
        <div class="position-absolute w-100 h-100 d-flex flex-column justify-content-between p-2 top-0 left-0 position-relative" >
            <div class="p-4">
                <h3 class="row text-white pb-2">{projects[selectedProject].title}</h3>
                <div class="row" bind:this={projectImageContainer}>
                    <p class="col-7 p-0 text-white">{projects[selectedProject].text}</p>
                    <div class="col-5"></div>
                    <img class="h-50 position-absolute top-50 shadowDarker p-0 projectImage" id="0" style="transform: translateY(-50%); border-radius: 8px;" src="{projects[selectedProject].src}" alt="">
                </div>
                <svg class="position-absolute w-100 h-100" style="top: 0; left: 0; z-index: -1; border-radius: 3%;" id="visual" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"><defs><filter id="blur1" x="-10%" y="-10%" width="120%" height="120%"><feFlood flood-opacity="0" result="BackgroundImageFix"></feFlood><feBlend mode="normal" in="SourceGraphic" in2="BackgroundImageFix" result="shape"></feBlend><feGaussianBlur stdDeviation="161" result="effect1_foregroundBlur"></feGaussianBlur></filter></defs><rect width="900" height="600" fill="#{projects[selectedProject].colorsHex[1]}"></rect><g filter="url(#blur1)">
                    <circle cx="877" cy="11" fill="#{projects[selectedProject].colorsHex[0]}" r="357"></circle><circle cx="259" cy="45" fill="#{projects[selectedProject].colorsHex[1]}" r="357"></circle><circle cx="866" cy="592" fill="#{projects[selectedProject].colorsHex[0]}" r="357"></circle><circle cx="743" cy="302" fill="#{projects[selectedProject].colorsHex[0]}" r="357"></circle><circle cx="288" cy="498" fill="#{projects[selectedProject].colorsHex[1]}" r="357"></circle><circle cx="192" cy="260" fill="#{projects[selectedProject].colorsHex[0]}" r="357"></circle></g></svg>
            </div>
            <div class="d-flex justify-content-between align-items-center ">
                <p class="m-0">Made with {projects[selectedProject].tech}</p>
                <a href="{projects[selectedProject].srcGithub}">
                    <img src="assets/github.svg" alt="github" height="30px" width="30px">
                </a>
            </div>
        </div>
    </div>
    <div class="col-2 ms-4">
        <div class="d-flex flex-column justify-content-end h-100">
            <div class="d-flex justify-content-center align-items-center w-100" style="gap: 10px;">
                    <small>{selectedProject+1}</small>
                    <div style="width: 80%; position: relative;">
                        <div class="w-100 rounded-3" style="height: 5px; background-color: var(--secondaryColor);"></div>
                        <div class="projectProgressBar position-absolute rounded-3 top-50" style="background-image: var(--gradient); width:  { percentProjectBar }%; height:8px; transform: translateY(-50%);"></div>
                    </div>
                    <small>{projects.length}</small>
            </div>
            <div class="d-flex justify-content-center mb-2">
                <button type="button" class="btn bg-transparent noHighLight" on:click={prevProject}>
                    <img src="assets/arrow.png" alt="left arrow(see previous project)">
                </button>
                <button type="button" class="btn btn-primary-outline noHighLight" on:click={nextProject}>
                    <img style="transform: rotate(180deg);" src="assets/arrow.png" alt="right arrow(see next project)">
                </button>
            </div>
        </div>
    </div>
</div>

<style>
   .projectProgressBar {
       transition: width 1s ease-in-out;
   }

   .noHighLight {
        outline: none;
        box-shadow: none;
        border: none;
   }

    .projectImage {
        left: 60%
    }



</style>