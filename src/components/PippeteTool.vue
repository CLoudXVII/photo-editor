<template>
  <div class="modal">
    <div class="modal__header header">
      <p class="header__text">Пипетка</p>
      <button class="header__button" @click="$emit('show')">
        <span class="cross">
          +
        </span>
      </button>
    </div>
    <div class="modal__container">
      <div class="modal__column">
      <div class="modal__element">
        <input class="color-button" type="radio" :style="color1"/>
      </div>
      <div class="modal__element" title="В модели CIE 1931 Y - это яркость, Z почти равно синему (в CIE RGB), а X представляет собой 
      сочетание трех кривых CIE RGB, выбранных неотрицательными">
        <p class="modal__heading">XYZ:</p>
        <p>{{ XYZ1 }}</p>
      </div>
      <div class="modal__element" title="Цветовое пространство RGB (Red, Green, Blue)">
        <p class="modal__heading">RGB:</p>
        <p>{{ RGB1 }}</p>
      </div>
      <div class="modal__element" title="Значения цвета в LAB задаются через светлоту (Lightness) и две координаты, отвечающие за хроматическую
      составляющую: тон и насыщенность.A — положение цвета в диапазоне от зелёного до красного, B — от синего до жёлтого.">
        <p class="modal__heading">Lab:</p>
        <p>{{ Lab1 }}</p>
      </div>
      <div class="modal__element">
        <p class="modal__heading">X:</p>
        <p>{{ X1 }}</p>
      </div>
      <div class="modal__element">
        <p class="modal__heading">Y:</p>
        <p>{{ Y1 }}</p>
      </div>
    </div>
    <div class="modal__column">
      <div class="modal__element">
        <input class="color-button" type="radio" :style="color2"/>
      </div>
      <div class="modal__element" title="В модели CIE 1931 Y - это яркость, Z почти равно синему (в CIE RGB), а X представляет собой 
      сочетание трех кривых CIE RGB, выбранных неотрицательными">
        <p class="modal__heading">XYZ:</p>
        <p>{{ XYZ2 }}</p>
      </div>
      <div class="modal__element" title="Цветовое пространство RGB (Red, Green, Blue)">
        <p class="modal__heading">RGB:</p>
        <p>{{ RGB2 }}</p>
      </div>
      <div class="modal__element" title="Значения цвета в LAB задаются через светлоту (Lightness) и две координаты, отвечающие за хроматическую
      составляющую: тон и насыщенность.A — положение цвета в диапазоне от зелёного до красного, B — от синего до жёлтого.">
        <p class="modal__heading">Lab:</p>
        <p>{{ Lab2 }}</p>
      </div>
      <div class="modal__element">
        <p class="modal__heading">X:</p>
        <p>{{ X2 }}</p>
      </div>
      <div class="modal__element">
        <p class="modal__heading">Y:</p>
        <p>{{ Y2 }}</p>
      </div>
    </div>
    </div>
  </div>
</template>
<script>
export default {
  name: "PippeteeModal",
  props: {
    resl: Array,
    startX: Number,
    startY: Number,
    nowW: Number,
    nowH: Number,
  },
  components: {},
  data() {
    return {
      rgb: [],
      XYZ: [],
      color1: "",
      color2: "",
      isAlt: false,
      X1: 0,
      Y1: 0,
      X2: 0,
      Y2: 0,
      RGB1: 0,
      rgb1: [],
      RGB2: 0,
      rgb2: [],
      XYZ1: 0,
      XYZ2: 0,
      Lab1: 0,
      Lab2: 0,
    };
  },
  watch: {
    RGB: function (value) {
      if (this.isAlt) {
        this.color2 = `background: rgb(${value.string});`;
        this.RGB2 = value.string;
        this.rgb2 = value.array;
        this.XYZ2 = this.rgbToXyz(value.array);
        this.Lab2 = this.xyzToLab(this.XYZ);
      } else {
        this.color1 = `background: rgb(${value.string});`;
        this.RGB1 = value.string;
        this.rgb1 = value.array;
        this.XYZ1 = this.rgbToXyz(value.array);
        this.Lab1 = this.xyzToLab(this.XYZ);
      }
    },
    coords: function (value) {
      if (this.isAlt) {
        this.X2 = value.X;
        this.Y2 = value.Y;
      } else {
        this.X1 = value.X;
        this.Y1 = value.Y;
      }
    },
  },
  methods: {
    handlePressAlt(event) {
      if (event.key === "Alt") {
        this.isAlt = true;
      }
    },
    handleUnpressAlt(event) {
      if (event.key === "Alt") {
        this.isAlt = false;
      }
    },
    rgbToXyz(rgb) {
      let r = rgb[0];
      let g = rgb[1];
      let b = rgb[2];
      let _r = r / 255;
      let _g = g / 255;
      let _b = b / 255;

      // Применяем коррекцию гамма-компрессии sRGB
      _r = _r > 0.04045 ? Math.pow((_r + 0.055) / 1.055, 2.4) : _r / 12.92;
      _g = _g > 0.04045 ? Math.pow((_g + 0.055) / 1.055, 2.4) : _g / 12.92;
      _b = _b > 0.04045 ? Math.pow((_b + 0.055) / 1.055, 2.4) : _b / 12.92;

      _r *= 100;
      _g *= 100;
      _b *= 100;

      // Коэффициенты преобразования RGB в XYZ
      let x =
        Math.round((_r * 0.4124564 + _g * 0.3575761 + _b * 0.1804375) * 10) /
        10;
      let y =
        Math.round((_r * 0.2126729 + _g * 0.7151522 + _b * 0.072175) * 10) / 10;
      let z =
        Math.round((_r * 0.0193339 + _g * 0.119192 + _b * 0.9503041) * 10) / 10;

      this.XYZ = [x, y, z];

      return `${x}, ${y}, ${z}`;
    },
    xyzToLab(xyz) {
      const [x, y, z] = xyz;

      // Коэффициенты для преобразования
      const xn = 95.047;
      const yn = 100.0;
      const zn = 108.883;

      const fx = x / xn;
      const fy = y / yn;
      const fz = z / zn;

      const epsilon = 0.008856;
      const kappa = 903.3;

      const f = (t) =>
        t > epsilon ? Math.pow(t, 1 / 3) : (kappa * t + 16) / 116;

      const L = Math.round((116 * f(fy) - 16) * 10) / 10;
      const a = Math.round(500 * (f(fx) - f(fy)) * 10) / 10;
      const b = Math.round(200 * (f(fy) - f(fz)) * 10) / 10;

      return `${L}, ${a}, ${b}`;
    },
  },
  computed: {
    RGB() {
      if (this.resl != null) {
        return {
          array: [this.resl[0], this.resl[1], this.resl[2]],
          string: `${this.resl[0]}, ${this.resl[1]}, ${this.resl[2]}`,
        };
      } else {
        return 0;
      }
    },
    contrastRatio() {
      if (this.rgb1 != [] && this.rgb2 != []) {
        const sRGBToLinear = (c) => {
          c = c / 255;
          return c <= 0.03928 ? c / 12.92 : Math.pow((c + 0.055) / 1.055, 2.4);
        };

        const getLuminance = (color) => {
          const rgb = color;
          const r = sRGBToLinear(rgb[0]);
          const g = sRGBToLinear(rgb[1]);
          const b = sRGBToLinear(rgb[2]);
          return 0.2126 * r + 0.7152 * g + 0.0722 * b;
        };

        const luminance1 = getLuminance(this.rgb1);
        const luminance2 = getLuminance(this.rgb2);

        const maxLuminance = Math.max(luminance1, luminance2);
        const minLuminance = Math.min(luminance1, luminance2);

        const contrastRatio = (maxLuminance + 0.05) / (minLuminance + 0.05);

        return Math.round(contrastRatio * 10) / 10;
      } else {
        return 0;
      }
    },
    coords() {
      if (
        this.startX != null &&
        this.startX > 0 &&
        this.startX < this.nowW &&
        this.startY != null &&
        this.startY > 0 &&
        this.startY < this.nowH
      ) {
        return { X: this.startX, Y: this.startY };
      } else {
        return 0;
      }
    },
  },
  mounted() {
    window.addEventListener("keydown", this.handlePressAlt);
    window.addEventListener("keyup", this.handleUnpressAlt);
  },
};
</script>
<style lang="scss" scoped>
.modal {
  position: absolute;
  left: 210px;
  top: 70px;
  background-color: white;
  padding: 15px;
  border: 1px solid gray;
  border-radius: 10px;
  display: flex;
  flex-direction: column;
  row-gap: 7px;

  &__header {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
  }

  &__container {
    display: flex;
    flex-direction: row;
    align-items: center;
    column-gap: 16px;
  }

  &__column {
    display: flex;
    flex-direction: column;
    row-gap: 4px;
    width: 180px;
  }

  &__element {
    display: flex;
    flex-direction: row;
    column-gap: 8px;
    text-align: center;
    cursor: default;
  }

  &__heading {
    font-weight: 600;
  }
}

.color-button {
  appearance: none;
  cursor: pointer;
  border: 1px solid black;
  border-radius: 50px;
  width: 23px;
  height: 23px;

  &:checked {
    border: 2px solid black;
  }
}

.header {

  &__text {
    font-size: 20px;
  }

  &__button {
    background: none;
    border: none;
    font-size: 48px;
    cursor: pointer;
    transition: 0.3s;
  }

  &__button:hover {
    transform: rotate(90deg);
  }
}

.cross {
  display: block;
  transform: rotate(45deg);
  line-height: 0;
}

</style>
