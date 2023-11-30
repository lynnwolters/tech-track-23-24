<script>
    import { onMount } from 'svelte'
    import { gsap } from 'gsap/dist/gsap'
    import { ScrollTrigger } from 'gsap/dist/ScrollTrigger'

    onMount(async function() {
        gsap.registerPlugin(ScrollTrigger)

        ScrollTrigger.create({
            trigger: '.type-effect-conclusion-trigger',
            start: 'top top',
            end: '+=100%',
            // pin: true,
            onEnter: () => typeEffectConclusion(),
        })
    })

    function typeEffectConclusion() {
        const text = document.querySelector('.type-effect-conclusion')
        const characters = text.textContent.split('')

        text.textContent = ''

        gsap.to(text, { opacity: 1 })

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
</script>

<section class="type-effect-conclusion-trigger">
    
    <div>
        <h2 class="title-normal type-effect-conclusion">Conclusion</h2>
    </div>
    
    <div>
        <p class="p-text-normal">To sum up, the 2022 CBS study on online crime in the Netherlands gives us a clear picture of the challenges we face in the digital world. Beyond just numbers, it shows that online threats like scams/fraud, hacking, and threats/intimidation have real and profound impacts on individuals and society. <br> <br> The study underscores the need for action. People often become victims due to a lack of awareness, weak security practices, or underestimating online dangers. The key takeaway is that we must be proactive in understanding and protecting ourselves from these threats. <br> <br> So, let's take this as a wake-up call. By promoting cybersecurity education, adopting strong protective measures, and fostering a shared responsibility, we can collectively work to reduce the impact of online crime. For practical tips on staying safe online, check out the article <a target="_blank" href="https://laatjeniethackmaken.nl/">"Laat Je Niet Hack Maken"</a> Stay informed, be vigilant, and let's navigate the digital landscape with confidence.</p>
    </div>

</section>

<style>
    /* TITLE */
    section > div:nth-of-type(1) {
        margin: 2em 2em 2em 2em;
        height: 100vh;
        display: flex;
        justify-content: center;
        align-items: center;
        background-color: gray;
    }

    section > div:nth-of-type(1) h2 {
        text-align: center;
    }

    .type-effect-conclusion {
        opacity: 0;
    }

    /* TEXT */
    section div:nth-of-type(2) {
        height: 100vh;
        display: grid;
        align-items: center;
        grid-template-columns: repeat(12, 1fr);
        column-gap: 2em;
        margin: 2em 2em 2em 2em;
        background-color: gray;
    }

    section div:nth-of-type(2) p {
        grid-column-start: 4;
        grid-column-end: 10;
        text-align: center;
    }

    section div:nth-of-type(2) p a {
        color: var(--color-3);
    }

    @media (max-width: 800px) {
        section div:nth-of-type(2) p {
            grid-column-start: 3;
            grid-column-end: 11;
        }
    }

    @media (max-width: 560px) {
        section div:nth-of-type(2) p {
            grid-column-start: 1;
            grid-column-end: 13;
        }
    }
</style>