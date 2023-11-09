<header>
    <nav class="p-text-small">
        <p>By Lynn Wolters</p>
        <p>October, 2023</p>
    </nav>
    <h1 class="title-normal">The dangers <br> of the world <br> wide web</h1>
    <p class="p-text-normal">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. <br> <br> Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum. Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi.</p>
</header>

<style>
    /* HEADER */
    header {
        display: grid;
        grid-template-columns: repeat(12, 1fr);
        column-gap: 2em;
        margin: 2em 2em 12em 2em;
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
        margin: .6em 0;
        text-align: center;
    }

    /* TEXT */
    p {
        grid-column-start: 4;
        grid-column-end: 10;
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