<script>
    import { onMount } from 'svelte';

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

    function fade2(node, {
        delay = 0,
        duration = 800
        }) 
        {
            return {
                delay,
                duration,
                css: t => `opacity: ${t * 1};`
            };
            // display: ${(t > delay) ? 'unset' : 'none'};
    }


    let selectedProject = 0;
    let animationDirection;
    $ : percentProjectBar = ( (selectedProject + 1) / projects.length ) * 100;



    function nextProject() {
        if (selectedProject < projects.length - 1) {
            //Increment to new project index
            selectedProject++;
            animationDirection = "Right";
        }
    }

    function prevProject() {
        if (selectedProject > 0) {
            //Increment to prev project index
            selectedProject--;
            animationDirection = "Left";
        }
    }


    let card, bounds;

    function rotateToMouse(e) {
        bounds = card.getBoundingClientRect();

        const mouseX = e.clientX;
        const mouseY = e.clientY;
        const leftX = mouseX - bounds.x;
        const topY = mouseY - bounds.y;

        const center = {
            x: leftX - bounds.width / 2,
            y: topY - bounds.height / 2
        }
        const distance = Math.sqrt(center.x**2 + center.y**2);
        
        card.style.transform = `
        scale3d(1.05, 1.05,1.05)
        rotateX(${topY/bounds.height * 15 - 7.5}deg)
        rotateY(${(leftX/bounds.width * 15 - 7.5)*-1}deg)
        `;
        console.log("🚀 ~ file: ProjectCard.svelte ~ line 82 ~ rotateToMouse ~ card.style.transform", topY/bounds.height)
        
        // card.classList.remove("top-50");
        
        
    }

    // function mouseEntered(){
    //     bounds = card.getBoundingClientRect();
    //     console.log("entered");
    //     document.addEventListener('mousemove', rotateToMouse);
    // }

    function mouseLeft(){
        card.style.transform = 'rotateX(0deg) rotateY(0deg)';
        card.style.background = '';
        console.log("left");
    }

    onMount(() => {
        card = document.querySelector('.projectImage');
        bounds = card.getBoundingClientRect();
        console.log("🚀 ~ file: ProjectCard.svelte ~ line 96 ~ onMount ~ card", card)
    });


</script>
<div style="margin: 10vh 0;" class="projectShowcaseWrapper">
    <h1 class="text-center my-5">See my work</h1>
    <div class="d-flex justify-content-center overflow-hidden">
        <div class="position-relative container m-0 col-8" style="height:0;width:30%;padding-bottom:30%;">
            <div class="position-absolute w-100 h-100 d-flex flex-column justify-content-between p-2 top-0 left-0 position-relative" >
                <div class="p-4">
                    {#each projects as project, i}
                        {#if i == selectedProject}
                            <h3 class="row pb-2 text-white"  in:fade2="{{delay: 1000}}">{project.title}</h3>
                        {/if}
                    {/each}
                    <div class="row">
                        {#each projects as project, i}
                            {#if i == selectedProject}
                                <p class="col-7 p-0 text-white" in:fade2="{{delay: 1200}}">{project.text}</p>
                            {/if}
                        {/each}        
                        <div class="col-5"></div>
                        {#each projects as projectImage, i}
                            <div class="h-50 position-absolute p-0 
                            fitter
                            {(selectedProject == i && animationDirection == "Left" ) ? 'c_slideInLeft' : ''}
                            {(selectedProject == i && animationDirection == "Right" ) ? 'c_slideInRight' : ''}
                            {(selectedProject != i && animationDirection == "Left" ) ? 'c_slideOutRight' : ''}
                            {(selectedProject != i && animationDirection == "Right" ) ? 'c_slideOutLeft' : ''}
                            {(selectedProject != i && animationDirection == null ) ? 'outside' : ''}" style="width: fit-content; perspective: 1000px;">
                                <img class="h-100 projectImage shadowDarker"
                                style=" border-radius: 8px;" src="{projectImage.src}" alt="" on:mouseleave={mouseLeft} on:mousemove={rotateToMouse}>
                            </div>
                        {/each}
                    </div>
                    <svg class="position-absolute w-100 h-100 backgroundSquare" style="top: 0; left: 0; z-index: -1; border-radius: 3%;" id="visual" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"><defs><filter id="blur1" x="-10%" y="-10%" width="120%" height="120%"><feFlood flood-opacity="0" result="BackgroundImageFix"></feFlood><feBlend mode="normal" in="SourceGraphic" in2="BackgroundImageFix" result="shape"></feBlend><feGaussianBlur stdDeviation="161" result="effect1_foregroundBlur"></feGaussianBlur></filter></defs><rect width="900" height="600" fill="#{projects[selectedProject].colorsHex[1]}"></rect><g filter="url(#blur1)">
                        <circle cx="877" cy="11" fill="#{projects[selectedProject].colorsHex[0]}" r="357"></circle><circle cx="259" cy="45" fill="#{projects[selectedProject].colorsHex[1]}" r="357"></circle><circle cx="866" cy="592" fill="#{projects[selectedProject].colorsHex[0]}" r="357"></circle><circle cx="743" cy="302" fill="#{projects[selectedProject].colorsHex[0]}" r="357"></circle><circle cx="288" cy="498" fill="#{projects[selectedProject].colorsHex[1]}" r="357"></circle><circle cx="192" cy="260" fill="#{projects[selectedProject].colorsHex[0]}" r="357"></circle></g>
                    </svg>
                </div>
                {#each projects as project, i}
                    {#if i == selectedProject}
                        <div class="d-flex justify-content-between align-items-center "  in:fade2="{{delay: 1400}}">
                            <p class="m-0">Made with {project.tech}</p>
                            <a href="{project.srcGithub}">
                                <img src="assets/github.svg" alt="github" height="30px" width="30px">
                            </a>
                        </div>
                    {/if}
                {/each}
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
</div>

<style>
    .projectShowcaseWrapper {
        
    }

   .projectProgressBar {
       transition: width 1s ease-in-out;
   }

   .noHighLight {
        outline: none;
        box-shadow: none;
        border: none;
   }

    .projectImage {
        transition-duration: 300ms;
        transition-property: transform, box-shadow;
        transition-timing-function: ease-out;
    }

    .fitter {
        left: 60%;
        transition-duration: 1s;
        transition-property: box-shadow;
    }
    .fitter:hover {
        box-shadow: 0 0 20px 5px #0000002a, #00000044 0 0 20px inset;
    }

    .backgroundSquare circle,  .backgroundSquare rect {
        transition: fill 0.5s linear 1s;
    }

    .outside {
        left: 100vw;
    }


</style>