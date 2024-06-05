<script lang="ts">
    import Canvas from "./Canvas.svelte";
	import Foo from "./Foo.svelte";

    let isDragging: boolean = false;
    function stopDivisionFollow(e: Event) {
        pauseEvent(e)
        console.log("stopDivisionFollow");
        isDragging = false;
    }

    function pauseEvent(e: Event){  // Duplicated in Canvas.svelte
        if(e.stopPropagation) e.stopPropagation();
        if(e.preventDefault) e.preventDefault();
        e.cancelBubble=true;
        e.returnValue=false;
        return false;
    }

    let showMenu = "";
    function toggleRightMenu() {
        if (showMenu == "show-right") {
            showMenu = "";
        } else {
            showMenu = "show-right";
        }
    }
    function toggleDownMenu() {
        if (showMenu == "show-down") {
            showMenu = "";
        } else {
            showMenu = "show-down";
        }
    }
</script>

<!-- svelte-ignore a11y-no-static-element-interactions -->
<div id="root" class="{showMenu}" on:mouseup={stopDivisionFollow} on:touchend={stopDivisionFollow} on:touchcancel={stopDivisionFollow}>
    <div id="canvas">
        <Canvas bind:isDragging={isDragging} />
    </div>

    <button id="menu-right-switch" on:click={toggleRightMenu}>--------</button>
    <div id="menu-right">
        Menu right
    </div>

    <button id="menu-down-switch" on:click={toggleDownMenu}>--------</button>
    <div id="menu-down">
        Menu down
    </div>
</div>

<style>
    :global(*) {
        margin: 0;
        padding: 0;
        min-width: 0;
        min-height: 0;
        overflow: hidden;

        box-sizing: border-box;

        --unit: min(calc(100vh / 10), calc(100vw / 16));
        /* --unit: calc(100vw / 16);
        --unit: calc(100vh / 10); */

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

        --show-right: 0;
        --show-down: 0;

        --right: calc(var(--unit) * 3.667 * var(--show-right));
        --down: calc(var(--unit) * 2.667 * var(--show-down));

        --transition-sync: 0.15s ease-in-out;
    }
    #root.show-right {
        --show-right: 1;
    }
    #root.show-down {
        --show-down: 1;
    }
    @media only screen and (orientation: portrait) {
        #root {
            --right: 0;
            --down: calc(var(--unit) * 16 * 2/3);
        }
        :global(body) {
            overflow: scroll;
        }
    }

    #canvas {
        width: calc(100% - var(--right));
        height: calc(100% - var(--down));
        transition: all var(--transition-sync);

        position: relative;

        border: 2px solid lightblue;
        box-sizing: border-box;
    }

    #menu-right, #menu-down {
        position: absolute;
        background-color: var(--background-color);
        transition: all var(--transition-sync);
    }

    #menu-right {
        top: 0;
        right: 0;

        width: var(--right);
        height: 100vh;
    }
    #menu-down {
        bottom: 0;
        left: 0;

        height: var(--down);
        width: 100%;
    }
    @media only screen and (orientation: portrait) {
        #menu-down {
            /* height: calc(var(--down) * 2);
            bottom: calc(var(--down) * -1); */
            overflow-y: auto;
        }
    }

    #menu-right-switch, #menu-down-switch {
        position: absolute;

        --a: calc(var(--unit) * 3/4);
        --b: calc(var(--unit) / 5.667);

        background-color: var(--accent-color);
        color: var(--background-color);
        border: none;
        /* z-index: 1; */

        transition: all var(--transition-sync);
    }
    #menu-right-switch {
        width: var(--b);
        height: var(--a);

        top: calc(var(--unit) * 3/4);
        left: calc(100% - var(--right) - var(--b));

        border-radius: calc(var(--b) * 2/3) 0 0 calc(var(--b) * 2/3);
    }
    #menu-down-switch {
        width: var(--a);
        height: var(--b);

        top: calc(100vh - var(--down) - var(--b));
        left: calc(var(--unit) * 3/4);
        
        border-radius: calc(var(--b) * 2/3) calc(var(--b) * 2/3) 0 0;
    }
    #menu-right-switch:hover, #menu-down-switch:hover {
        filter: brightness(1.1);

        --scale: calc(5/4);
    }
    #menu-right-switch:hover {
        width: calc(var(--b) * var(--scale));
        left: calc(100% - var(--right) - var(--b) * var(--scale));
    }
    #menu-down-switch:hover {
        height: calc(var(--b) * var(--scale));
        top: calc(100vh - var(--down) - var(--b) * var(--scale));
    }
</style>