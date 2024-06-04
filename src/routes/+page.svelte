<script lang="ts">
    import Canvas from "./Canvas.svelte";
	import Foo from "./Foo.svelte";

    let x: number = 5;
</script>

<div id="root">
    <div id="canvas">
        <Canvas />
    </div>
    <div id="menu-right">Menu right</div>
    <div id="menu-down">Menu down</div>
</div>

<style>
    :global(*) {
        margin: 0;
        padding: 0;
        min-width: 0;
        min-height: 0;
        overflow: hidden;

        box-sizing: border-box;

        --background-color: rgb(8, 24, 48);
        --accent-color: lightBlue;
        --accent-color-r: 173;
        --accent-color-g: 216;
        --accent-color-b: 230;
        --font-color: lightBlue;

        color: var(--font-color);

        /* background-color: var(--background-color); */
    }
    :global(body) {
        background-size: 100% 100%;
	    background-position: 0px 0px,0px 0px;
        background-image: linear-gradient(20deg, #00001488 0%, #000014 0%, #00142888 70%, #7F400088 100%),linear-gradient(301deg, #112700FF 0%, #400072FF 100%);
    }

    #root {
        height: 100vh;
        width: 100%;

        --right: 360px;
        --down: 240px;
    }

    #canvas {
        width: calc(100% - var(--right));
        height: calc(100% - var(--down));

        position: relative;

        border: 2px solid lightblue;
        box-sizing: border-box;
    }

    #menu-right {
        position: absolute;
        top: 0;
        right: 0;

        width: var(--right);
        height: 100vh;
    }

    #menu-down {
        position: absolute;
        bottom: 0;
        left: 0;

        height: var(--down);
        width: 100%;
    }
</style>