<script lang="ts">
    enum CanvasMode {
        LeftRight,
        TopDown,
        Compare
    }

    let activeMode: CanvasMode = CanvasMode.Compare;

    let imageLeftSrc: string = "https://letsenhance.io/static/8f5e523ee6b2479e26ecc91b9c25261e/1015f/MainAfter.jpg";
    let imageRightSrc: string = "https://h5p.org/sites/default/files/h5p/content/1209180/images/file-6113d5f8845dc.jpeg";

    let dividerPositionValue: number = 0.5;

    let isDragging: boolean = false;
    function startDivisionFollow() {
        console.log("startDivisionFollow");
        isDragging = true;
    }
    function stopDivisionFollow() {
        console.log("stopDivisionFollow");
        isDragging = false;
    }

    let canvas = document.getElementById("canvas");
    function updateDividerPosition(event: MouseEvent) {
        if (isDragging) {
            // console.log("updateDividerPosition");

            if (canvas == null) {
                return;
            }

            let canvasRect = canvas.getBoundingClientRect();

            let dividerPosition = (event.clientX - canvasRect.left) / canvasRect.width;
            dividerPosition = Math.max(Math.min(dividerPosition, canvasRect.width), 0);

            if (Math.abs(dividerPosition - 0.5) < 0.01) {
                dividerPosition = 0.5;
            }

            dividerPositionValue = dividerPosition;
            // console.log(dividerPositionValue);
        }
    }
</script>

<!-- svelte-ignore a11y-no-static-element-interactions -->
<div id="root" style="--divider-position-value: {dividerPositionValue}" on:mousemove={updateDividerPosition} on:mouseup={stopDivisionFollow}>
    <div id="image-left-viewing-window">
        <div id="image-left" class="image-container">
            <img src="{imageLeftSrc}" alt="left" />
        </div>
    </div>
    <div id="divider" on:mousedown={startDivisionFollow}></div>
    <div id="image-right-viewing-window">
        <div id="image-right" class="image-container">
            <img src="{imageRightSrc}" alt="right" />
        </div>
    </div>
    <div id="overlay">
        
    </div>
</div>

<style>
    #root {
        position: relative;
        width: 100%;
        height: 100%;

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
    #root * {
        background-color: transparent;
    }
    #root img {
        width: 800px;
        height: 400px;
        /* object-fit: contain; */
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
        backdrop-filter: blur(8px);

        cursor: ew-resize;

        z-index: 1;
        overflow: visible;
    }
    #divider::after {
        --segment-width: 6px;
        --repeat: 10px;

        content: "";
        display: inline-block;

        position: relative;

        top: calc(-15360px + var(--holder-size) - 100% / 8);
        left: calc(var(--holder-size) - var(--holder-size) / 2 - 18px);

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
    }
    #divider:hover, #divider:hover::before, #divider:hover::after {
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

    #image-left {
        width: calc(100% / var(--divider-position-value));
    }
    #image-right {
        position: relative;

        --proportion: calc(var(--divider-position-value) / (1 - var(--divider-position-value)));
        left: calc(-100% * var(--proportion));

        width: calc(100% / (1 - var(--divider-position-value)));
    }
</style>