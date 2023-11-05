<section>

        <div>
            <h2>Conclusion</h2>
        </div>
        <div>
            <p>
                Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. 
                <br> <br>
                Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum. Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae.
            </p>
        </div>

</section>

<style>
    section {
        margin: 0 2em 0 2em;
    }

    section div:nth-of-type(1) {
        height: 50vh;
        display: flex;
        justify-content: center;
        align-items: center;
    }

    section div:nth-of-type(1) h2 {
        color: var(--color-2);
        font-family: 'Perfect DOS VGA 437';
        font-size: 9em;
        letter-spacing: -.05em;
        text-transform: uppercase;
        line-height: 80%;
    }

    section div:nth-of-type(2) {
        height: 50vh;
        display: grid;
        grid-template-columns: repeat(12, 1fr);
        gap: 2em;
    }

    section div:nth-of-type(2) p {
        grid-column-start: 4;
        grid-column-end: 10;
        color: var(--color-3);
        font-family: 'Coder\'s Crux';
        font-size: 1.8em;
        text-transform: uppercase;
        line-height: 70%;
    }
</style>