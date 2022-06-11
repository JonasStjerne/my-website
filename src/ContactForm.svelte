<script>
    let responeMessage;
    let loading = false;

    function submitForm() {
        console.log("sending..");
        loading = true;
        grecaptcha.ready(function() {
            grecaptcha.execute('6Ldyql4gAAAAAM5gAXa0PZcWFHFSFm-5M82H5Y-A', {action: 'submit'}).then(function(token) {
                document.getElementById("g-token").value = token;

                const request = new XMLHttpRequest();

                request.open("post", "https://us-central1-personal-website-65dab.cloudfunctions.net/widgets/sendMessage");
                

                request.setRequestHeader("Content-Type", "application/json");

                request.onload = function () {
                   const { errors } = JSON.parse(request.responseText);
                   responeMessage = errors[0].msg;
                   loading = false;
                }

                const data = new FormData(document.getElementById("contactForm"));
               
                request.send(JSON.stringify(Object.fromEntries(data.entries())));
            });
        });
    }

    function testsubmitForm(){
        console.log("sending...");
        loading = true;
        setTimeout(() => {
            loading = false;
        }, 2000)
    }
    

</script>
<div class="w-100 my-5" data-aos="fade-up" data-aos-duration="800" data-aos-offset="200">
    <div class="row justify-content-center w-100 m-0">
        <form on:submit|preventDefault={!loading && submitForm} id="contactForm" method="POST" class="d-flex flex-column flex alig-items-center col-12 col-md-8 col-lg-6 shadow p-5"
        style="border-radius: 10px; gap:20px; background-color: white;">
            <input type="hidden" id="g-token" name="g-token">
            <h2 class="text-center">Get in touch</h2>
            <input required type="text" style="border-radius: 5px;" id="name" name="name" placeholder="Name">
            <input required type="email" style="border-radius: 5px;" id="email" name="email" placeholder="Email">
            <textarea name="message" id="message" cols="30" rows="10" required style="resize:none; border-radius: 5px;" placeholder="Message"></textarea>
            <button type="submit" class="w-80 round text-white" style="background-image: var(--gradient); border-radius: 5px;" >Send</button>
            {#if responeMessage}
                <p class="text-center ">{responeMessage}</p>
            {:else if loading}
                <p class="text-center ">Sending...</p>
            {/if}
        </form>
    </div>
</div>