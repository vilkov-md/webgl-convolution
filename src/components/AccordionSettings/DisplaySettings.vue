<template>
    <div id="display-settings">
        <label for='color'>Color: </label>
        <input id='color' type='color' v-model="hexColor" @change="changeColor()">

        <button type='button' v-on:click="randomize()">Randomize Color</button>
        <br>
        <label for='skip' :title="skip_tooltip">Skip Frames: </label>
        <input id='skip' type='checkbox' v-model="skip_frames" :title="skip_tooltip" @change="setSkipFrames()">
        <button type='button' v-if="skip_frames" v-on:click="changeSkippedFrame()">Skip {{even_odd_txt}} frames</button>
        <br>
        <label for='persistent' :title="persistent_tooltip">Persistent pixels: </label>
        <input id='persistent' type='checkbox' v-model="persistent" :title="persistent_tooltip" @change="setPersistent()">
        <br>
        <label for='randomize-color' :title="randomize_tooltip">Always randomize color: </label>
        <input id='randomize-color' type='checkbox' v-model="always_randomize" :title="randomize_tooltip">
    </div>
</template>

<script>
import Controller from '../../js/controller';
import Utils from '../../js/utils';

export default {
    name: 'DisplaySettings',
    data() {
        return {
            rgbColor: [0, 0, 0],
            hexColor: '#000000',
            skip_frames: false,
            persistent: false,
            always_randomize: true,
            skip_tooltip: `If true, every other frame won't render, which helps reduce flashing.`,
            even_odd_tooltip: `Switches which frame is skipped`,
            even_odd_txt: 'even',
            persistent_tooltip: 'If true, once pixel values are set to non-zero values they are permanently fixed',
            randomize_tooltip: 'If true, the color is randomized when the "Randomize" button is pressed',

        }
    },

    mounted() {
        this.rgbColor = Controller.color;
        this.hexColor = this.rgbToHex(Controller.color);
    },

    methods: {
        randomize() {
            this.rgbColor = Utils.randomColor();
            this.hexColor = this.rgbToHex(this.rgbColor);
            this.changeColor();
        },

        changeColor() {
            Controller.color = this.hexToRgb(this.hexColor);
            Controller.apply();
        },

        setColor(col) {
            this.rgbColor = col;
            this.hexColor = this.rgbToHex(col);
        },

        hexToRgb(hex) {
            let result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
            return result ? [
                parseInt(result[1], 16) / 255,
                parseInt(result[2], 16) / 255,
                parseInt(result[3], 16) / 255
            ] : null;
        },

        rgbToHex(rgb) {
            let r = Math.ceil(rgb[0]*255);
            let g = Math.ceil(rgb[1]*255);
            let b = Math.ceil(rgb[2]*255);
            return "#" + ((1 << 24) + (r << 16) + (g << 8) + b).toString(16).slice(1);
        },
        
        setPersistent() {
            Controller.setPersistent(this.persistent)
        },

        setSkipFrames() {
            Controller.renderer.skip_frames = this.skip_frames;
        },

        changeSkippedFrame() {
            Controller.offsetSkippedFrame();
            this.even_odd_txt = this.even_odd_txt === 'odd' ? 'even' : 'odd';
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#display-settings {
    margin: 10px;
    text-align: left;
}

#color {
    background-color: rgb(61, 21, 112);
    border: 1px black solid;
}

#skip {
    margin-top: 10px;
}

</style>
