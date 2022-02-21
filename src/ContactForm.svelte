<script>
    let responeMessage;
    function submitForm() {
        grecaptcha.ready(function() {
            grecaptcha.execute('6LfMGWMeAAAAABozdCIiE0gMNyJXqAZFOyiZ1WF7', {action: 'submit'}).then(function(token) {
                document.getElementById("g-token").value = token;

                const request = new XMLHttpRequest();

                request.open("post", "http://localhost/my_website_backend/sendForm.php");

                request.onload = function () {
                    responeMessage = request.responseText;
                }

                const data = document.getElementById("contactForm");

                request.send(new FormData(data));

                
                // document.getElementById("contactForm").submit();
            });
        });
    }
        // action="http://localhost/my_website_backend/sendForm.php"
</script>
<div class="w-100 my-5" data-aos="fade-up" data-aos-duration="800" data-aos-offset="200">
    <div class="row justify-content-center w-100 m-0">
        <form on:submit|preventDefault={submitForm} id="contactForm" method="POST" class="d-flex flex-column flex alig-items-center col-12 col-md-8 col-lg-6 shadow p-5"
        style="border-radius: 10px; gap:20px; background-color: white;">
            <input type="hidden" id="g-token" name="g-token">
            <h2 class="text-center">Get in touch</h2>
            <input required type="text" style="border-radius: 5px;" name="name" placeholder="Name">
            <input required type="email" style="border-radius: 5px;" name="email" placeholder="Email">
            <textarea name="message" id="" cols="30" rows="10" required style="resize:none; border-radius: 5px;" placeholder="Message"></textarea>
            <button type="submit" class="w-80 round text-white" style="background-image: var(--gradient); border-radius: 5px;" >Send</button>
            {#if responeMessage}
                <p class="text-center ">{responeMessage}</p>
            {/if}
        </form>
    </div>
</div>