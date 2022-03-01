<script>
import { onMount } from "svelte";
import * as AOS from 'aos';
import "../node_modules/aos/dist/aos.css";
import 'animate.css';
import '../node_modules/bootstrap/dist/css/bootstrap.min.css';
import DeviceDetector from "svelte-device-detector";

import Skills from './Skills.svelte';
import MeModel from './MeModel.svelte';
import ProjectCard from './ProjectCard.svelte';
import ContactForm from "./ContactForm.svelte";
import LoadingScreen from "./LoadingScreen.svelte";

let meModelLoaded, skillsLoaded, loadElements = false;
onMount(() => {

	AOS.init();
	setTimeout(() => {
		loadElements = true;
	}, 1500)
})

function addEventListnersText() {
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
}


function handleLoad(element) {
	if (element.detail == "meModel") { 
		meModelLoaded = true;
		addEventListnersText();
		document.querySelector('body').style="overflow: visible; width: 100vw; height: 100vh;"

	} else if ( element.detail == "skills") {
		skillsLoaded = true;
	} 
}

function mobileLoad() {
	meModelLoaded = true;
}

</script>
<DeviceDetector showInDevice="desktop">
	<LoadingScreen loaded={meModelLoaded}/>
</DeviceDetector>
{ #if loadElements }
	<main>
		<div class="position-relative vh-50">
			<div id="header">
				<!-- https://codepen.io/frebliklo/pen/YeqaNB -->
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
			<div class="d-flex justify-content-center flex-md-row pb-5 w-100">
				<div class="content">
					<div class="introContainer">
						<h1 class="{meModelLoaded ? ' animate__animated animate__fadeIn animate__delay-2s' : ''}" aria-label="Hi">
							<span aria-hidden="true">
							H</span><span aria-hidden="true">i</span>
						</h1>
						<div class="{meModelLoaded ? ' animate__animated animate__fadeIn animate__delay-3s nameContainer rounded' : 'nameContainer rounded'}">
							<h2 class="nameEl font-weight-bold" aria-label="I'm Jonas">
								{#each "I'm Jonas" as char}
									{#if char == " "}
										&#160
									{:else}
										<span aria-hidden="true">{char}</span>
									{/if}
								{/each}
							</h2>
						</div>
						<p class="underText" class:animate__animated="{meModelLoaded}" class:animate__fadeIn="{meModelLoaded}" class:animate__delay-4s="{meModelLoaded}">
							An IT student passionate about building awesome full stack software solutions
							<!-- {#each "Currently studying IT at Aalborg University and working at" as char}
								{#if char == " "}
									&#160
								{:else}
									<span>{char}</span>
								{/if}
							{/each}
							< href="https://Openomic</a>.dk/">
								Openomic
							</a> 
							{#each "as a full stack developer" as char}
								{#if char == " "}
									&#160
								{:else}
									<span>{char}</span>
								{/if}
							{/each} -->
						</p>
					</div>
				</div>
				<DeviceDetector showInDevice="desktop">
					<MeModel on:loaded={handleLoad}/>
				</DeviceDetector>
			</div>
			<div id="waveBackground" style="background-image: url('assets/wave.png');" class="d-none d-md-block"></div>
			<img class="d-block d-md-none img-fluid w-100" src="assets/mobileWave.png" alt="">
			<!-- <div id="waveMobileBackground" style="background-image: url('assets/mobileWave.png');" class="d-block d-md-none"></div> -->
		</div>
		<div class="container-fluid p-0" style="background-color: var(--mainColor); margin-top: -2px; padding-top: 1px;">
			<div class="row py-5 py-md-0 g-0 w-100">
				<div class="col-12 col-md-6 d-flex  justify-content-center position-relative">
					<h3 class="skillsText text-white text-center m-auto text-md-start" data-aos="fade-in" data-aos-duration="800">I work in alot of different technolgies and love learning new ones
						{#if meModelLoaded}
							<div class="position-absolute w-100 h-100 start-0 top-0 underlineTarget"></div>
						{/if}
						<DeviceDetector showInDevice="mobile">
							<div class="position-absolute w-100 h-100 start-0 top-0 underlineTarget"></div>
						</DeviceDetector>
					</h3>
				</div>
				<div class="col-12 col-md-6 p-0" id="skillsWrapper">
					<Skills/>
				</div>
			</div>
			<div class="row my-5 text-white d-flex flex-column align-items-center w-100">
				<div class="col-10 col-md-8 col-lg-6"  data-aos="fade-up" data-aos-duration="800">
					<h3 class="w-100">About me</h3>
					<div>
						<p>
							My name is Jonas Stjerne I'm a student on my 4th semester in Informations Technology at Aalborg University in Denmark. I’m passionate about building IT solutions with a solid businees foundation. My interests includes a wide variety of things related to IT including UI design, front & backend development! <br>
							I strive to build the web of the future with a core focus on user expirence. I'm always interested in learning new technolgies and be the best at what I do.</p> <br>
							<p>I’m currently working <a class="text-white" style="text-decoration: none;" href="https://openomic.dk/">@Openomic</a> as a full stack junior developer & doing freelance.</p><br>
							<p>Feel free to <a class="text-white" href="#contactForm">contact me</a> if you have any questions or would like me to work on a project</p>
					</div>
				</div>
			</div>
			<img class="w-100" style="margin-bottom: -2px;" src="assets/multiWaveDivider.png"  alt="background wave" aria-hidden="true">
		</div>
		<ProjectCard/>
		<div class="postion-relative py-5" style="background-image: url('assets/bottomWave.png'); background-repeat: no-repeat; background-size: contain; background-position: bottom">
			<ContactForm/>
		</div>
	</main>
{/if}

<style>

	span {
		height:fit-content;
		animation-duration: 1s;
		display: inline-block;
	}

	/* main {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 5vh;
	} */

	#header {
		display: flex;
		justify-content: right;
		gap: 20px;
		width: 100%;
		padding-top: 10px;
	}

	#header > :last-child{
		padding-right: 20px;
	}

	#waveBackground {
		aspect-ratio: 1463.5/694.96;
		max-width: 100%;
		background-repeat: no-repeat;
		inset:0;
		z-index: -1;
		height: 100%;
		position: absolute;
		background-size: cover;
	}

	.skillsText {
		max-width: 600px;
		display: inline-block;
	}

	.underlineTarget::after {
		position: absolute;
		bottom: -15px;
		left: 0;
		height: 5px;
		content: "";
		visibility: visible;
		border-radius: 20px;
		background: var(--gradient);
		animation: underlineAnimation 1s cubic-bezier(0, 0, 0, 1.04) forwards 2s;
	}


	@media only screen and (max-width: 768px) {
		.underlineTarget::after {
			transform: translateX(-50%);
			left: 50%;
		}
	}

	@keyframes underlineAnimation {
		from {
			width: 0%;
		}

		to {
			width: 80%;
		}
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

