<div><span>Scale</span></div>
<div><span>Filter</span></div>
<div><span>HDR</span></div>
<div><span>Crop</span></div>

<style>
    div {
        display: flex;
        justify-content: center;
        align-items: center;

        height: 100%;
        width: 100%;

        background-color: lightblue;
        filter: brightness(0.667);
    }
    div:first-child {
        filter: brightness(0.8);
    }
    div:hover {
        filter: brightness(0.75);
    }
    div:first-child:hover {
        filter: brightness(0.9);
    }

    span {
        text-align: center;

        overflow: visible;
        color: var(--background-color);
    }
</style>