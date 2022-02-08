<script>

</script>

<div class="position-fixed" style="width: 100vw; height: 100vw; top: 0; left: 0; background-color: white; z-index: 10;">
    <div style="background-color: #171717; position: absolute; bottom: 0; width:100%; z-index:10;" id="fadeUpBaground"></div>
    <img src="assets/jsLogoWhite.png" alt="Logo" style="z-index: 20; position: absolute; left: 50%; top:50%; transform: translate(-50%,-50%); min-width: 100px; width: 15vw;">
</div>

<style>
    #fadeUpBaground {
        animation: fillOut 2s ease-out forwards 1s;
    }

    @keyframes fillOut {
        0% {
            height: 0;
        }

        100% {
            height: 100%;
        }
    }
</style>