<script>
    import { onMount } from 'svelte'
    import { gsap } from 'gsap/dist/gsap'
    import { ScrollTrigger } from 'gsap/dist/ScrollTrigger'

    onMount(async function () {
        gsap.registerPlugin(ScrollTrigger)
        typeEffectHeader()
        // slideIn()
    })

    function typeEffectHeader() {
        const text = document.querySelector('.type-effect-header')
        const characters = text.textContent.split('')

        text.textContent = ''

        characters.forEach((char, index) => {
            const span = document.createElement('span')
            span.textContent = char
            span.style.opacity = 0
            text.appendChild(span)

            gsap.to(span, {
                opacity: 1,
                duration: 0,
                delay: index * 0.075,
                onComplete: () => {
                    if (index === characters.length - 1) {
                        const cursor = document.createElement('span')
                        cursor.textContent = '|'
                        cursor.classList.add('cursor')
                        text.appendChild(cursor)

                        gsap.to(cursor, { opacity: 0, repeat: -1, yoyo: true, duration: 0.7 })
                    }
                }
            })
        })
    }

    // function slideIn() {
    //     const intro = document.querySelector('.slide-in-header')

    //     gsap.from(intro, {
    //         opacity: 0,
    //         y: 50,
    //         duration: 1,
    //         scrollTrigger: {
    //             trigger: 'header',
    //             start: 'top top',
    //             end: '+=300',
    //             scrub: 1
    //         }
    //     })
    // }
</script>

<header>
    <nav class="p-text-small">
        <p>By Lynn Wolters</p>
        <p>October, 2023</p>
    </nav>
    <h1 class="title-normal type-effect-header">The dangers <br> of the world <br> wide web</h1>
    <p class="p-text-normal slide-in-header">
        In 2022, the Netherlands Central Bureau of Statistics (CBS) delved into online crime, offering a detailed look at digital crime. This article summarizes key findings from the CBS report, focusing on three main types of online crime: scams/fraud, hacking, and intimidation/threats.
        <br> <br>
        Let's explore the CBS study results, uncovering the risks in the digital realm, how these threats impact people's mental well-being and why people become a victim of online crime.</p>
</header>

<style>
    /* HEADER */
    header {
        display: grid;
        grid-template-columns: repeat(12, 1fr);
        column-gap: 2em;
        margin: 2em 2em 2em 2em;
    }

    /* NAV */
    nav {
        grid-column-start: 1;
        grid-column-end: 13;
        display: flex;
        justify-content: space-between;
    }

    /* TITLE */
    h1 {
        grid-column-start: 2;
        grid-column-end: 12;
        margin: 1em 0;
        text-align: center;
    }

    /* TEXT */
    p {
        grid-column-start: 4;
        grid-column-end: 10;
        text-align: center;
    }

    @media (max-width: 800px) {
        h1 {
            grid-column-start: 1;
            grid-column-end: 13;
        }

        p {
            grid-column-start: 3;
            grid-column-end: 11;
        }
    }

    @media (max-width: 560px) {
        p {
            grid-column-start: 1;
            grid-column-end: 13;
        }
    }
</style>