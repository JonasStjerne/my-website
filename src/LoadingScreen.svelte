<script>
    export let loaded;
</script>

<div class="position-fixed {loaded === true ? 'dissapear' : ''}" style="width: 100vw; height: 100vh; top: 0; left: 0; z-index: 20;">
    <div class="w-100 h-100 d-flex justify-content-center align-items-center" style="background-color: {loaded === true ? 'transparent' : 'white'}; z-index: 10;">
        <img src="assets/jsLogoWhite.png" alt="Logo" style="min-width: 100px; width: 15vw; postion relative; margin: auto; z-index: 20; animation-fill-mode: forwards;" class="{loaded === true ? 'animate__bounceOut' : 'animate__animated animate__bounce' } ">
    </div>

    <div style="background-color: #171717; position: absolute; width:100%; z-index:10;" class="{loaded === true ? 'loadDone' : 'fadeUpBaground' }">
    </div>
</div>

<style>
    .fadeUpBaground {
        animation: fillOut 1s ease-in-out forwards 0.5s;
        bottom: 0;
    }

    .loadDone {
        animation: foldUp 1s ease-in-out forwards;
        bottom: unset;
        top: 0;
        height: 100%;
    }

    .dissapear {
        animation: dissapear 0.1s ease forwards 1s;
    }


    @keyframes fillOut {
        0% {
            height: 0%;
        }

        100% {
            height: 100%;
        }
    }

    @keyframes foldUp {
        0% {
            height: 100%;
        }

        100% {
            height: 0%;
        }
    }

    @keyframes dissapear {
        0% {
            top: 0;
        }

        100% {
            top: -100vh;
        }
    }


    .animate__animated.animate__bounce {
        animation-iteration-count: infinite;
        animation-duration: 2s;
    }
    

</style>