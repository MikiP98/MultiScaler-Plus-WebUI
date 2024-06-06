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
    Algorithm parameters
    --- \/
    <!-- If one is used by selected algorithms is is displayed without te down arrow -->
     <span>
        <label for="sharpness">Sharpness:</label>
        <input type="range" id="sharpness" name="sharpness" min="0" max="1" step="0.01" />
        <span>0.5</span>
     </span>
     <span>
        <label for="nedi_m">NEDI m:</label>
        <input type="number" id="nedi_m" name="nedi_m" min="1" step="1" />
     </span>
</span>
<span>
    Options
    --- \/
    <span>
        <label for="output_format">Output file format:</label>
        <select id="output_format" name="output_format">
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
</span>
<span>
    <button on:click={testGetScales}>Test getScales</button>
</span>

<style>
    .hidden {
        display: none !important;
        visibility: hidden !important;
    }
</style>