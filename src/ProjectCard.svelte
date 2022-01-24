<script>
    export let ImgSrc, ImgAlt, Title, Text, Tech, GithubLink; 
</script>
<div class="wrapper">'
    <img class="thumbnail" src="{ImgSrc}" alt="{ImgAlt}">
    <div class="container">
        <h2>{Title}</h2>
        <p>{Text}</p>
        <small>{Tech}</small>
        <div>
            <a class="githubLinkContainer" href="{GithubLink}"><small class="githubLink">View on Github</small><img src="assets/github.svg" alt="Github logo" height="30"></a>
        </div>
    </div>
</div>

<style>
    .wrapper {
        box-shadow: 0px 0px 16px 5px rgba(0, 0, 0, 0.19);
        border-radius: 5px;
        min-height: 20vh;
        width: 30vw;
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .container {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: space-between;
        margin: 10px 30px;
        height: 100%;
    }

    .thumbnail {
        margin-top: -50px;
        position: relative;
        max-width: 25vw;
        border-radius: 2;
    }

    .githubLinkContainer {
        display: flex;
        justify-content: center;
        align-items: center;
    }

</style>