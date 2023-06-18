<template>
  <div class="color-picker">
    <h1>Color Picker</h1>

    <div class="controls">
      <label>
        <input type="radio" v-model="mode" value="rgb" />
        RGB
      </label>
      <label>
        <input type="radio" v-model="mode" value="hex" />
        HEX
      </label>
      <label>
        <input type="radio" v-model="mode" value="hsl" />
        HSL
      </label>
    </div>

    <div class="sliders">
      <div v-if="mode === 'rgb'" class="rgb-controls">
        <label class="label-1">Red:</label>
        <input
          class="slider-1"
          type="range"
          v-model="rgb.red"
          min="0"
          max="255"
        />
        <input
          class="input-1 input-number"
          type="number"
          v-model.number="rgb.red"
          min="0"
          max="255"
        />

        <label class="label-2">Green:</label>
        <input
          class="slider-2"
          type="range"
          v-model="rgb.green"
          min="0"
          max="255"
        />
        <input
          class="input-2 input-number"
          type="number"
          v-model.number="rgb.green"
          min="0"
          max="255"
        />

        <label class="label-3">Blue:</label>
        <input
          class="slider-3"
          type="range"
          v-model="rgb.blue"
          min="0"
          max="255"
        />
        <input
          class="input-3 input-number"
          type="number"
          v-model.number="rgb.blue"
          min="0"
          max="255"
        />
      </div>

      <div v-else-if="mode === 'hex'" class="hex-controls">
        <label class="label-1">R:</label>
        <input
          class="slider-1"
          type="range"
          v-model.number="rgb.red"
          min="0"
          max="255"
        />
        <input class="input-1 input-number" type="text" v-model="red" />

        <label class="label-2">G:</label>
        <input
          class="slider-2"
          type="range"
          v-model.number="rgb.green"
          min="0"
          max="255"
        />
        <input class="input-2 input-number" type="text" v-model="green" />

        <label class="label-3">B:</label>
        <input
          class="slider-3"
          type="range"
          v-model.number="rgb.blue"
          min="0"
          max="255"
        />
        <input class="input-3 input-number" type="text" v-model="blue" />
      </div>

      <div v-else-if="mode === 'hsl'" class="hsl-controls">
        <label class="label-1">Hue:</label>
        <input
          class="slider-1"
          type="range"
          v-model.number="hsl.hue"
          min="0"
          max="360"
        />
        <input
          class="input-1 input-number"
          type="number"
          v-model.number="hsl.hue"
          min="0"
          max="360"
        />

        <label class="label-2">Saturation:</label>
        <input
          class="slider-2"
          type="range"
          v-model.number="hsl.saturation"
          min="0"
          max="100"
        />
        <input
          class="input-2 input-number"
          type="number"
          v-model.number="hsl.saturation"
          min="0"
          max="100"
        />

        <label class="label-3">Lightness:</label>
        <input
          class="slider-3"
          type="range"
          v-model.number="hsl.lightness"
          min="0"
          max="100"
        />
        <input
          class="input-3 input-number"
          type="number"
          v-model.number="hsl.lightness"
          min="0"
          max="100"
        />
      </div>
    </div>

    <div class="color-preview" :style="colorStyle"></div>

    <div class="output">
      <label>Output:</label>
      <input ref="outputField" type="text" :value="colorOutput" readonly />
      <button @click="copyToClipboard">Copy</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      mode: "rgb",
      rgb: {
        red: 0,
        green: 0,
        blue: 0,
      },

      hsl: {
        hue: 0,
        saturation: 0,
        lightness: 0,
      },
    };
  },
  computed: {
    colorStyle() {
      if (this.mode === "rgb") {
        return `background-color: rgb(${this.rgb.red}, ${this.rgb.green}, ${this.rgb.blue})`;
      } else if (this.mode === "hex") {
        return `background-color: ${this.colorOutput}`;
      } else if (this.mode === "hsl") {
        return `background-color: hsl(${this.hsl.hue}, ${this.hsl.saturation}%, ${this.hsl.lightness}%)`;
      }
      return "";
    },
    colorOutput() {
      if (this.mode === "rgb") {
        return `rgb(${this.rgb.red}, ${this.rgb.green}, ${this.rgb.blue})`;
      } else if (this.mode === "hex") {
        return `#${this.red}${this.green}${this.blue}`;
      } else if (this.mode === "hsl") {
        return `hsl(${this.hsl.hue}, ${this.hsl.saturation}%, ${this.hsl.lightness}%)`;
      }
      return "";
    },
    red: {
      get() {
        return this.rgbToHex(this.rgb.red);
      },
      set(value) {
        this.rgb.red = this.hexToRgb(value);
      },
    },
    green: {
      get() {
        return this.rgbToHex(this.rgb.green);
      },
      set(value) {
        this.rgb.green = this.hexToRgb(value);
      },
    },
    blue: {
      get() {
        return this.rgbToHex(this.rgb.blue);
      },
      set(value) {
        this.rgb.blue = this.hexToRgb(value);
      },
    },
  },
  watch: {
    mode(newMode, oldMode) {
      this.updateSliders(newMode, oldMode);
    },
  },

  methods: {
    updateSliders(newMode, oldMode) {
      if (oldMode === "rgb" || (oldMode === "hex" && newMode === "hsl")) {
        const { hue, saturation, lightness } = this.rgbToHsl(
          this.rgb.red,
          this.rgb.green,
          this.rgb.blue
        );

        this.hsl = {
          hue: Math.round(hue),
          saturation: Math.round(saturation),
          lightness: Math.round(lightness),
        };
      } else if (oldMode === "hsl") {
        this.rgb = this.hslToRgb(
          this.hsl.hue,
          this.hsl.saturation,
          this.hsl.lightness
        );
      }
    },

    copyToClipboard() {
      const outputField = this.$refs.outputField;
      outputField.select();
      document.execCommand("copy");
      outputField.setSelectionRange(0, 0);
    },

    rgbToHex(value) {
      const hexValue = Math.round(value).toString(16).toUpperCase();
      return hexValue.padStart(2, "0");
    },

    rgbToHsl(r, g, b) {
      r /= 255;
      g /= 255;
      b /= 255;

      const max = Math.max(r, g, b);
      const min = Math.min(r, g, b);
      let h,
        s,
        l = (max + min) / 2;

      if (max === min) {
        h = s = 0; // achromatic
      } else {
        const d = max - min;
        s = l > 0.5 ? d / (2 - max - min) : d / (max + min);
        switch (max) {
          case r:
            h = (g - b) / d + (g < b ? 6 : 0);
            break;
          case g:
            h = (b - r) / d + 2;
            break;
          case b:
            h = (r - g) / d + 4;
            break;
        }
        h /= 6;
      }

      return {
        hue: h * 360,
        saturation: s * 100,
        lightness: l * 100,
      };
    },

    hslToRgb(hue, saturation, lightness) {
      hue /= 360;
      saturation /= 100;
      lightness /= 100;

      let red, green, blue;

      if (saturation === 0) {
        red = green = blue = lightness;
      } else {
        const hueToRgb = (p, q, t) => {
          if (t < 0) t += 1;
          if (t > 1) t -= 1;
          if (t < 1 / 6) return p + (q - p) * 6 * t;
          if (t < 1 / 2) return q;
          if (t < 2 / 3) return p + (q - p) * (2 / 3 - t) * 6;
          return p;
        };

        const q =
          lightness < 0.5
            ? lightness * (1 + saturation)
            : lightness + saturation - lightness * saturation;
        const p = 2 * lightness - q;

        red = hueToRgb(p, q, hue + 1 / 3);
        green = hueToRgb(p, q, hue);
        blue = hueToRgb(p, q, hue - 1 / 3);
      }

      return {
        red: Math.round(red * 255),
        green: Math.round(green * 255),
        blue: Math.round(blue * 255),
      };
    },

    hexToRgb(hex) {
      hex = hex.replace("#", "");
      const decimalValue = parseInt(hex, 16);
      const clampedValue = Math.min(Math.max(decimalValue, 0), 255);
      return clampedValue;
    },
  },
};
</script>

<style scoped>
.color-picker {
  max-width: 400px;
  margin: 0 auto;
  text-align: center;
  padding: 20px;
}

.controls {
  margin-bottom: 20px;
}

.rgb-controls,
.hsl-controls,
.hex-controls {
  display: grid;
  grid-template-areas:
    "label-1 slider-1 input-1"
    "label-2 slider-2 input-2"
    "label-3 slider-3 input-3";
  gap: 10px;
  align-items: center;
}

.label-1 {
  grid-area: label-1;
  justify-self: end;
}

.slider-1 {
  grid-area: slider-1;
}

.input-1 {
  grid-area: input-1;
}

.label-2 {
  grid-area: label-2;
  justify-self: end;
}

.slider-2 {
  grid-area: slider-2;
}

.input-2 {
  grid-area: input-2;
}

.label-3 {
  grid-area: label-3;
  justify-self: end;
}

.slider-3 {
  grid-area: slider-3;
}

.input-3 {
  grid-area: input-3;
}

.input-number {
  width: 40px;
}

.color-preview {
  width: 200px;
  height: 200px;
  margin: 20px auto 0 auto;
  border: 2px solid #000;
  margin-bottom: 20px;
}

.output {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-top: 10px;
}

.output label {
  margin-right: 10px;
}

.output input[type="text"] {
  width: 200px;
}

.output button {
  margin-left: 10px;
}
</style>
