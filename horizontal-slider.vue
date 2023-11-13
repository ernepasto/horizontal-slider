<template>
    <div>

        <div class="slider">
            <div class="slider-dots">
                <span @click="goToSlide($event)" class="dot active-dot"></span>
                <span @click="goToSlide($event)" class="dot"></span>
            </div>
            <div class="slide active-slide header-slide">
                <div class="slide-container">
                    <div class="slide-pretitle">Hello, I'm Ernesto</div>
                    <div class="slide-title">I'm a web developer and cybersecurity expert</div>
                    <div class="slide-cta">Get in touch</div>
                </div>
            </div>
            <div class="slide">
                <div class="slide-container">
                    <div class="slide-title">Privatezza e protezione dei dati</div>
                    <p class="slide-text">
                        Considered discovered ye sentiments projecting 
                        entreaties of melancholy is. In expression an 
                        solicitude principles in do. Hard do me sigh with 
                        west same lady. Their saved linen downs tears son 
                        add music.
                    </p>
                </div>
            </div>
        </div>


    </div>
</template>


<script setup>
import { onMounted, ref } from 'vue';
import gsap from 'gsap';
import SplitType from 'split-type';

let slides = ref(null);
let activeSlide = ref(null);
let dots = ref(null);
let activeDot = ref(null);

let activeIndex = ref(0);

// Funzione per l'animazione del contenuto della slide
const animateSlide = () => {
    let slideTitle = activeSlide.value.querySelector('.slide-title');
    let slideText = activeSlide.value.querySelector('.slide-text');

    // Divisione del titolo in parole
    let words = new SplitType(slideTitle, { type: 'word' }).words;

    const slideTimeline = gsap.timeline();
    slideTimeline.from(words, {
        duration: .8,
        y: -20,
        clipPath: 'polygon(0% 100%, 100% 100%, 100% 100%, 0% 100%)',
        ease: 'power4',
        stagger: .15
    });
    slideTimeline.from(slideText, {
        duration: .8,
        y: -10,
        clipPath: 'polygon(0% 100%, 100% 100%, 100% 100%, 0% 100%)',
        ease: 'power4',
    }, '-=.8');
};

// Funzione per l'animazione del punto attivo
const animateDot = () => {
    gsap.from(activeDot.value, {
        duration: .7,
        ease: 'power4',
        scale: 0
    });
};

// Funzione per navigare tramite i punti
const goToSlide = (event) => {
    // Individuazione della slide richiesta
    let dotsArray = Array.from(dots.value);
    let slideIndex = dotsArray.indexOf(event.target);

    if (slideIndex === activeIndex.value) return;

    // Aggiornamento dell'indice attivo
    activeIndex.value = slideIndex;

    // Aggiornamento della slide attiva 
    activeSlide.value.classList.remove('active-slide');
    slides.value[slideIndex].classList.add('active-slide');
    activeSlide.value = slides.value[slideIndex];

    // Animazione della slide che viene mostrata
    animateSlide();

    // Cambio del punto attivo
    activeDot.value.classList.remove('active-dot');
    dots.value[slideIndex].classList.add('active-dot');
    activeDot.value = dots.value[slideIndex];

    // Animazione del punto attivo
    animateDot();
};

onMounted(() => {

    slides.value = document.querySelectorAll('.slide');
    activeSlide.value = document.querySelector('.active-slide');
    
    dots.value = document.querySelectorAll('.slider-dots .dot');
    activeDot.value = document.querySelector('.active-dot');

    // Animazione della slide iniziale 
    let words = new SplitType(activeSlide.value.querySelector('.slide-title'), { type: 'word' }).words;
    const slideTimeline = gsap.timeline();
    slideTimeline.from(words, {
        delay: .65,
        duration: .8,
        y: -20,
        clipPath: 'polygon(0% 100%, 100% 100%, 100% 100%, 0% 100%)',
        ease: 'power4',
        stagger: .15
    });

    // Individuazione dell'indice della slide attiva al momento
    activeIndex.value = 0;
    slides.value.forEach((slide, index) => {
        if (slide.classList.contains('active-slide')){
            activeIndex.value = index;
        } 
    });

    // Dichiarazione della slide precedente
    let previusSlide = null;
    if (slides.value[activeIndex.value - 1]) { // Verifica dell'esistenza di una slide precedente
        previusSlide = slides.value[activeIndex.value - 1];
    }

    // Dichiarazione della slide successiva
    let nextSlide = null;
    if (slides.value[activeIndex.value + 1]) { // Verifica dell'esistenza di una slide successiva
        nextSlide = slides.value[activeIndex.value + 1];
    }

    let debounceTimer;

    onwheel = (event) => {

        // Utilizzo della tecnica di debouncing per ridurrre la frequenza con cui l'eventListener viene richiamato
        clearTimeout(debounceTimer);
        debounceTimer = setTimeout(() => {

            // Blocco del cambio slide nel caso di animazione in corso
            if (gsap.isTweening('.slide-title .word')) return;

            //let snap = gsap.utils.snap(15);
            //console.log(snap(event.deltaY));
            //if (snap(event.deltaY) < 15) return;

            // Ricalcolo di 'nextSlide' nel caso in cui venga premuto un punto per la navigazione
            nextSlide = slides.value[activeIndex.value + 1];

            if (event.deltaY > 0) { // Bisogna andare alla slide successiva
                // Se esiste la slide successiva, avviene l'aggiornamento della slide attiva 
                if (nextSlide) {
                    activeSlide.value.classList.remove('active-slide');
                    nextSlide.classList.add('active-slide');
                    activeSlide.value = nextSlide;

                    // Animazione della slide che viene mostrata
                    animateSlide();

                    // Aggiornamento dell'indice attivo
                    activeIndex.value = activeIndex.value + 1;

                    // Cambio del punto attivo
                    activeDot.value.classList.remove('active-dot');
                    dots.value[activeIndex.value].classList.add('active-dot');
                    activeDot.value = dots.value[activeIndex.value];

                    // Animazione del punto attivo
                    animateDot();
                }
            } else { // Bisogna andare alla slide precedente
                // Ricalcolo di 'previusSlide' nel caso in cui venga premuto un punto per la navigazione
                previusSlide = slides.value[activeIndex.value - 1];

                // Se esiste la slide precedente, avviene l'aggiornamento della slide attiva 
                if (previusSlide) {
                    activeSlide.value.classList.remove('active-slide');
                    previusSlide.classList.add('active-slide');
                    activeSlide.value = previusSlide;

                    // Animazione della slide che viene mostrata
                    animateSlide();

                    // Aggiornamento dell'indice attivo
                    activeIndex.value = activeIndex.value - 1;

                    // Cambio del punto attivo
                    activeDot.value.classList.remove('active-dot');
                    dots.value[activeIndex.value].classList.add('active-dot');
                    activeDot.value = dots.value[activeIndex.value];

                    // Animazione del punto attivo
                    animateDot();
                }
            }

            // Se esiste la slide successiva, viene aggiornato il valore di 'nextSLide' 
            if (slides.value[activeIndex.value + 1]) {
                nextSlide = slides.value[activeIndex.value + 1];
            } else {
                nextSlide = null;
            }

            // Se esiste la slide precedente, viene aggiornato il valore di 'previusSlide' 
            if (slides.value[activeIndex.value - 1]) {
                previusSlide = slides.value[activeIndex.value - 1];
            } else {
                previusSlide = null;
            }

        }, 10);
    };
});


</script>


<style lang="scss">
@import '../assets/scss/_variables';


.slider {
    height: 100vh;
    width: 100%;
    position: relative;
    overflow: hidden;
    background: $colorPrimary;
    .slider-dots {
        position: absolute;
        bottom: 20px;
        right: 20px;
        span {
            cursor: pointer;
            height: 10px;
            width: 10px;
            margin-bottom: 5px;
            display: block;
            border: solid 1px $colorBgBlack;
            border-radius: 100px;
            background: transparent;
            will-change: scale;
            &.active-dot {
                background: $colorBgBlack;
            }
            &:last-child {
                margin: 0;
            }
        }
    }
    .slide {
        height: 100%;
        width: 100%;
        display: none;
        &.active-slide {
            display: block;
        }
        .slide-container {
            height: 100%;
            width: 100%;
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            .slide-title {
                width: 100%;
                max-width: 675px;
                margin: 0 0 .2em 0;
                color: $colorTextBlack;
                font-size: 3em;
                text-align: center;
                font-kerning: none;
                .word {
                    clip-path: polygon(0 0, 100% 0, 100% 100%, 0% 100%);
                }
            }
            .slide-text {
                width: 100%;
                max-width: 675px;
                margin: 0;
                color: $colorTextBlack;
                font-size: 1.15em;
                line-height: 140%;
                clip-path: polygon(0 0, 100% 0, 100% 100%, 0% 100%);
            }
        }
    }
}

.slider {
    .slide.header-slide {
        .slide-pretitle {
            margin: 0 0 .9em 0;
            color: $colorTextBlack;
            font-size: 1.45em;
            font-weight: 500;
        }
        .slide-title {
            width: 100%;
            max-width: 750px;
            margin: 0 auto .5em auto;
            color: $colorTextBlack;
            font-size: 3.7em;
            font-family: 'watchquinn';
            line-height: 108%;
        }
        .slide-cta {
            cursor: pointer;
            height: 50px;
            width: 150px;
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            border-radius: 100px;
            background: $colorBgBlack;
            color: $colorTextWhite;
            font-size: .9em;
        }
    }
}

</style>