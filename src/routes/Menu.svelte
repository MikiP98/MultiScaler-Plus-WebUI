<script lang="ts">
    let presets = ["none", "photo", "pixel_art", "de_jpeg"];
    let preset_custom_names: any = {  // Any because of line 11
        "de_jpeg": "De-JPEG",
    }

    let preset = presets[0];

    function displayPreset(p: string) {
        if (preset_custom_names.hasOwnProperty(p)) {
            return preset_custom_names[p];
        }
        return p.split('_').map((s) => s.charAt(0).toUpperCase() + s.slice(1)).join(' ');
    }

    let allow_multiple = false;
    let scale = 2;
    let scales = '';
    function setScale(s: number) {
        scale = s;
    }
    function getScales() {
        if (allow_multiple) {
            return scales.replaceAll('_', '').replaceAll(',', ' ').replaceAll('  ', ' ') .split(' ').map((s) => parseFloat(s)).filter(item => !isNaN(item));
        } else {
            return [scale];
        }
    }

    function testGetScales() {
        console.log(getScales());
    }

    let show_additional_algorithm_parameters = false;
    function toggleAdditionalAlgorithmParameters() {
        show_additional_algorithm_parameters = !show_additional_algorithm_parameters;
    }

    let is_sharpness_required = false;
    let sharpness = 0.5;

    let is_nedi_m_required = false;
    let nedi_m = 4;

    let show_more_options = false;
    function toggleMoreOptions() {
        show_more_options = !show_more_options;
    }

    let output_format = 'webp';
</script>

<span>
    <label for="preset">Preset:</label>
    <select id="preset" name="preset" bind:value={preset}>
        {#each presets as p}
            <option value={p}>{displayPreset(p)}</option>
        {/each}
    </select>
</span>
<span>
    <label for="scale">Scale{allow_multiple ? 's' : ''}:</label>
    <button on:click={() => {setScale(0.5)}}>0.5</button>
    <button on:click={() => {setScale(2)}}>2</button>
    <button on:click={() => {setScale(4)}}>4</button>
    <input type="number" id="scale" name="scale" min="0" step="0.25" bind:value={scale} class="{allow_multiple ? 'hidden' : ''}" />
    <input type="text" id="scales" name="scales" bind:value={scales} class="{allow_multiple ? '' : 'hidden'}" />
    <!-- <input type="range" id="scale" name="scale" min="0" max="4" step="0.125" bind:value={scale} /> -->
</span>
<span>
    Algorithm:
</span>
<span>
    <br>
    Algorithm parameters <br>

    <span class:hidden={!is_sharpness_required}>
        <label for="sharpness">Sharpness:</label>
        <input type="range" id="sharpness" name="sharpness" min="0" max="1" step="0.01" bind:value={sharpness} />
        <span>{sharpness}</span>
    </span>
    <span class:hidden={!is_nedi_m_required}>
        <label for="nedi_m">NEDI M:</label>
        <input type="number" id="nedi_m" name="nedi_m" min="1" step="1" bind:value={nedi_m} />
    </span>

    <button on:click={toggleAdditionalAlgorithmParameters}> <!-- If one is used by selected algorithms is is displayed without te down arrow -->
        --- <span class="list_arrow" class:rotated={show_additional_algorithm_parameters}>&gt</span>
    </button>

    <div id="additional-algorithm-parameters" class:show-hidden-algorithm-parameters={show_additional_algorithm_parameters}>
        <span class:hidden={is_sharpness_required}>
            <label for="sharpness">Sharpness:</label>
            <input type="range" id="sharpness" name="sharpness" min="0" max="1" step="0.01" bind:value={sharpness} />
            <span>{sharpness}</span>
        </span>
        <span class:hidden={is_nedi_m_required}>
            <label for="nedi_m">NEDI m:</label>
            <input type="number" id="nedi_m" name="nedi_m" min="1" step="1" bind:value={nedi_m} />
        </span>
    </div>
</span>
<span>
    <br>
    More Options <br>
    <button on:click={toggleMoreOptions}>--- <span class="list_arrow" class:rotated={show_more_options}>&gt</span></button>
    <div id="more-options" class:show-more-options={show_more_options}>
        <span>
            <label for="output_format">Output file format:</label>
            <select id="output_format" name="output_format" bind:value={output_format}>
                <option value="jpeg_xl">JPEG XL</option>
                <option value="avif">AVIF</option>
                <option value="png">PNG</option>
                <option value="webp">WebP</option>
                <option value="qoi">QOI</option>
            </select>
        </span>
        <span>
            Allow multiple:
            <span>
                <label for="allow_multiple">scales</label>
                <input type="checkbox" id="allow_multiple" name="allow_multiple" bind:checked={allow_multiple} />
            </span>
            <span>
                <label for="allow_multiple">algorithms</label>
                <input type="checkbox" id="allow_multiple" name="allow_multiple" bind:checked={allow_multiple} />
            </span>
            <span>
                <label for="allow_multiple">file formats</label>
                <input type="checkbox" id="allow_multiple" name="allow_multiple" bind:checked={allow_multiple} />
            </span>
        </span>
        <span>
            <label for="lossless">Lossless compression:</label>
            <input type="checkbox" id="lossless" name="lossless" />
        </span>
        <span>
            <label for="additional_lossless">Additional lossless compression:</label>
            <input type="checkbox" id="additional_lossless" name="additional_lossless" />
        </span>
    </div>
</span>
<span>
    <button on:click={testGetScales}>Test getScales</button>
</span>

<style>
    .hidden {
        display: none !important;
        visibility: hidden !important;
    }
    .list_arrow {
        display: inline-block;
        transform: rotate(-90deg);
        transition: transform 0.15s ease-out;
    }
    button:hover .list_arrow {
        transform: rotate(90deg);
    }
    .list_arrow.rotated {
        transform: rotate(90deg)
    }
    button:hover .list_arrow.rotated {
        transform: rotate(-90deg);
    }

    #additional-algorithm-parameters, #more-options {
        height: 0;
        padding-left: 10%;
        font-size: calc(var(--unit) / 6.5);

        transition: all 0.1s ease-out;

        display: flex;
        flex-direction: column;
    }

    .show-hidden-algorithm-parameters {
        height: 64px !important;
    }
    .show-more-options {
        height: 128px !important;
    }
</style>