<script lang="ts">
    enum CanvasMode {
        LeftRight,
        TopDown,
        Compare
    }
    const CanvasModeToString = {
        [CanvasMode.LeftRight]: "mode-left-right",
        [CanvasMode.TopDown]: "mode-top-down",
        [CanvasMode.Compare]: "mode-compare"
    }

    let activeMode: CanvasMode = CanvasMode.Compare;
    let activeModeString: string = "mode-compare";

    let imageLeftSrc: string = "https://letsenhance.io/static/8f5e523ee6b2479e26ecc91b9c25261e/1015f/MainAfter.jpg";
    let imageRightSrc: string = "https://h5p.org/sites/default/files/h5p/content/1209180/images/file-6113d5f8845dc.jpeg";

    let dividerPositionValue: number = 0.5;
    let dividerActive = "active";

    export let isDragging: boolean = false;
    function startDivisionFollow(e: Event) {
        if (activeMode == CanvasMode.Compare) {
            pauseEvent(e)
            console.log("startDivisionFollow");
            isDragging = true;
        }
    }

    let canvas: HTMLElement | null = null;
    function updateDividerPositionMouse(event: MouseEvent) {
        if (isDragging) {
            pauseEvent(event);
            console.log("updateDividerPosition");

            if (canvas == null) {
                canvas = document.getElementById("canvas");
                if (canvas == null) {
                    return;
                }
            }
            updateDividerPosition(canvas, event.clientX);
        }
    }
    function updateDividerPositionTouch(event: TouchEvent) {
        if (isDragging) {
            pauseEvent(event);
            console.log("updateDividerPosition");

            if (canvas == null) {
                canvas = document.getElementById("canvas");
                if (canvas == null) {
                    return;
                }
            }
            updateDividerPosition(canvas, event.touches[0].clientX);
        }
    }
    function updateDividerPosition(canvas: HTMLElement, eventX: number) {
        if (isDragging) {
            let canvasRect = canvas.getBoundingClientRect();

            let dividerPosition = (eventX - canvasRect.left) / canvasRect.width;
            dividerPosition = Math.max(Math.min(dividerPosition, canvasRect.width), 0);

            if (Math.abs(dividerPosition - 0.5) < 0.0078125) {
                dividerPosition = 0.5;
            }

            dividerPositionValue = dividerPosition;
            // console.log(dividerPositionValue);
        }
    }

    function pauseEvent(e: Event){
        if(e.stopPropagation) e.stopPropagation();
        if(e.preventDefault) e.preventDefault();
        e.cancelBubble=true;
        e.returnValue=false;
        return false;
    }

    function setActiveMode(mode: CanvasMode) {
        activeMode = mode;
        activeModeString = CanvasModeToString[mode];
        if (activeMode != CanvasMode.Compare) {
            dividerPositionValue = 0.5;
            dividerActive = "";
        } else {
            dividerActive = "active";
        }
    }
</script>

<!-- svelte-ignore a11y-no-static-element-interactions -->
<div id="root" style="--divider-position-value: {dividerPositionValue}" on:mousemove={updateDividerPositionMouse} on:touchmove={updateDividerPositionTouch}>
    <div id="image-left-viewing-window">
        <div id="bg-image-left" class="bg-image {activeModeString}" style="--url: url({imageLeftSrc})"></div>
        <div id="image-left" class="image-container {activeModeString}">
            <img src="{imageLeftSrc}" alt="left" />
        </div>
    </div>
    <div id="divider" on:mousedown={startDivisionFollow} on:touchstart={startDivisionFollow} class="{dividerActive}"></div>
    <div id="image-right-viewing-window">
        <div id="bg-image-right" class="bg-image {activeModeString}" style="--url: url({imageRightSrc})"></div>
        <div id="image-right" class="image-container {activeModeString}">
            <img src="{imageRightSrc}" alt="right" />
        </div>
    </div>
    <div id="overlay">
        <div id="mode-button">
            <button on:click={() => setActiveMode(CanvasMode.LeftRight)}>Left-Right</button>
            <button on:click={() => setActiveMode(CanvasMode.TopDown)}>Top-Down</button>
            <button on:click={() => setActiveMode(CanvasMode.Compare)}>Compare</button>
        </div>
        <div id="controls-left"></div>
        <div id="controls-mid"></div>
        <div id="controls-right"></div>
    </div>
</div>

<style>
    #root {
        position: relative;
        width: 100%;
        height: 100%;

        box-shadow: inset 0px 0px calc(var(--unit) / 3) 0px black;

        --divider-position-value: 0.5;  /* Default 0.5, overwritten by JS */
        --divider-position: calc(100% * var(--divider-position-value));
    }
    #overlay, .image-container {
        position: absolute;
        width: 100%;
        height: 100%;

        display: flex;
        justify-content: center;
        align-items: center;
    }
    .bg-image {
        --url: url();
        --glow-coverage: 80%;

        position: absolute;
        width: 100%;
        height: 100%;

        background-image: var(--url);
        background-position: center;
        background-repeat: no-repeat;
        background-size: var(--glow-coverage) var(--glow-coverage);

        mask-image: radial-gradient(circle, black 0%, transparent var(--glow-coverage), transparent 100%);

        filter: blur(calc(var(--unit) * 7/12)) opacity(0.667);

        z-index: -1;
    }
    .image-container * {
        filter: none;
    }
    #root * {
        background-color: transparent;
    }
    #root img {
        width: 700px;
        height: 400px;
        /* object-fit: contain; */
        object-fit: cover;
    }

    #divider {
        --holder-size: 48px;
        --transparency: 0.4;
        --color: rgba(0, 0, 0, var(--transparency));
        --color-hover: rgba(
            var(--accent-color-r), 
            var(--accent-color-g), 
            var(--accent-color-b), 
            var(--transparency)
        );

        position: relative;

        content: "";
        position: absolute;

        margin-left: var(--divider-position);
        
        top: calc(var(--holder-size) / -2);
        left: calc(var(--holder-size) / -2);
        width: var(--holder-size);
        height: var(--holder-size);

        border-radius: 100%;

        background-color: var(--color);
        backdrop-filter: blur(8px); /* invert(1) hue-rotate(180deg) drop-shadow(0px 0px 12px var(--color)); */
        /* filter: opacity(var(--transparency)); */
        /* box-shadow: 0px 0px 65px 0.5px var(--color-hover); */

        z-index: 1;
        overflow: visible;
    }
    #divider.active {
        cursor: ew-resize;
        box-shadow: 0px 0px 65px 0.5px var(--color-hover);
    }
    #divider::after {
        --segment-width: 6px;
        --repeat: 10px;

        content: "";
        display: inline-block;

        position: relative;

        top: calc(-15360px + var(--holder-size) - 100% / 8);
        left: calc(var(--holder-size) - var(--holder-size) / 2 - 24px);

        width: 90%;
        height: 50%;

        /* background-color: var(--color); */
        background: repeating-linear-gradient(
                90deg, 
                transparent, 
                transparent var(--segment-width), 
                var(--color) var(--segment-width), 
                var(--color) var(--repeat)
            ) 0 0 / 100% 100% no-repeat;
        
        border-radius: 50%;

        filter: opacity(0.333) blur(1px);
        /* border-radius: 1px; */
        /* background-color: red; */
    }
    #divider::before {
        content: "";
        display: inline-block;

        position: relative;

        top: var(--holder-size);
        left: calc(var(--holder-size) / 2 + -0.5px);

        width: 1px;
        /* height: calc(100% - var(--holder-size) / 2); */
        height: 15360px;  /* 16k max resolution */

        /* background-color: var(--color); */
        background-color: var(--color);
        /* filter: invert(1) hue-rotate(180deg); */
    }
    #divider.active:hover, 
    #divider.active:hover::before, 
    #divider.active:hover::after, 
    #divider.active:active, 
    #divider.active:active::before, 
    #divider.active:active::after {
        background-color: var(--color-hover);
        /* backdrop-filter: invert(1) grayscale(1) blur(16px); */
        /* backdrop-filter: brightness(0.667) blur(8px); */
        box-shadow: 0px 0px 12px 1px var(--color-hover);
        /* filter: invert(0) hue-rotate(0); */
    }
    #divider.active:touchstart, 
    #divider.active:touchstart::before, 
    #divider.active:touchstart::after,
    #divider.active:touchmove, 
    #divider.active:touchmove::before, 
    #divider.active:touchmove::after {
        background-color: var(--color-hover);
        /* backdrop-filter: invert(1) grayscale(1) blur(16px); */
        /* backdrop-filter: brightness(0.667) blur(8px); */
        box-shadow: 0px 0px 12px 1px var(--color-hover);
    }

    #image-left-viewing-window, #image-right-viewing-window {
        position: absolute;
        height: 100%;
    }
    #image-left-viewing-window {
        width: var(--divider-position);
    }
    #image-right-viewing-window {
        margin-left: var(--divider-position);
        width: calc(100% - var(--divider-position));
    }

    #image-left.mode-compare, #bg-image-left.mode-compare {
        width: calc(100% / var(--divider-position-value));
    }
    #image-right.mode-compare {
        position: relative;
    }
    #image-right.mode-compare, #bg-image-right.mode-compare {
        --proportion: calc(var(--divider-position-value) / (1 - var(--divider-position-value)));
        left: round(down, calc(-100% * var(--proportion)), 1px);  /* round stops the image from unstable jitter by 1 px */

        width: calc(100% / (1 - var(--divider-position-value)));
    }

    #image-left.mode-left-right, #image-right.mode-left-right {
        overflow: auto;
    }

    #overlay {
        display: grid;
        grid-template-columns: repeat(16, 1fr);
        grid-template-rows: repeat(10, 1fr);
        --overlay-cell-height: ;

        padding: calc(var(--unit) / 2);
    }
    #mode-button {
        grid-column: 1;
        grid-row: 1;

        display: flex;
        justify-content: center;
        align-items: center;
    }
    #mode-button > button {
        --button-size: 48px;

        width: var(--button-size);
        height: var(--button-size);
    }
</style>