<header>
    <div>
        <h1>The dangers <br> of the world <br> wide web</h1>
    </div>
    <div>
        <p>By Lynn Wolters<p>
        <p>October, 2023</p>
    </div>
    <div>
        <p>
            Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. <br> <br>
            Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum. Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi. 
        </p>
    </div>
</header>

<style>
    header {
        display: flex;
        flex-direction: column;
        margin: 0 2em 0 2em;
    }

    header div:nth-of-type(1) {
        order: 2;
        display: grid;
        grid-template-columns: repeat(12, 1fr);
        gap: 2em;
        margin-top: 8%;
    }

    header div:nth-of-type(1) h1 {
        grid-column-start: 2;
        grid-column-end: 12;
        color: var(--color-2);
        font-family: 'Perfect DOS VGA 437';
        font-size: 9em;
        letter-spacing: -.05em;
        text-transform: uppercase;
        line-height: 80%;
        text-align: center;
    }

    header div:nth-of-type(2) {
        order: 1;
        display: flex;
        justify-content: space-between;
        margin-top: 2em;
    }

    header div:nth-of-type(2) p {
        color: var(--color-3);
        font-family: 'Coder\'s Crux';
        font-size: 1.25em;
        text-transform: uppercase;
        line-height: 70%;
    }

    header div:nth-of-type(3) {
        order: 3;
        display: grid;
        grid-template-columns: repeat(12, 1fr);
        gap: 2em;
        margin-top: 4%;
    }

    header div:nth-of-type(3) p {
        grid-column-start: 4;
        grid-column-end: 10;
        color: var(--color-3);
        font-family: 'Coder\'s Crux';
        font-size: 1.8em;
        text-transform: uppercase;
        line-height: 70%;
    }
</style>