<template>
  <div class="modal">
    <div class="modal__container header">
      <p class="header__text">Фильтрация</p>
      <button class="header__button" @click="$emit('show')">
        <span class="cross">+</span>
      </button>
    </div>
    <div class="modal__line">
      <div class="modal__element matrix">
        <div class="matrix__line">
          <input
            class="matrix__input"
            type="number"
            @change="changeX1"
            v-model="x1"
          />
          <input
            class="matrix__input"
            type="number"
            @change="changeX2"
            v-model="x2"
          />
          <input
            class="matrix__input"
            type="number"
            @change="changeX3"
            v-model="x3"
          />
        </div>
        <div class="matrix__line">
          <input
            class="matrix__input"
            type="number"
            @change="changeX4"
            v-model="x4"
          />
          <input
            class="matrix__input"
            type="number"
            @change="changeX5"
            v-model="x5"
          />
          <input
            class="matrix__input"
            type="number"
            @change="changeX6"
            v-model="x6"
          />
        </div>
        <div class="matrix__line">
          <input
            class="matrix__input"
            type="number"
            @change="changeX7"
            v-model="x7"
          />
          <input
            class="matrix__input"
            type="number"
            @change="changeX8"
            v-model="x8"
          />
          <input
            class="matrix__input"
            type="number"
            @change="changeX9"
            v-model="x9"
          />
        </div>
      </div>
    </div>
    <div class="modal__line">
      <div class="modal__element type">
        <label class="modal__label">
          <input type="radio" name="type" value="same" v-model="type"  />
          Тождественное отображение
        </label>
        <label class="modal__label">
          <input type="radio" name="type" value="sharp" v-model="type"  />
          Повышение резкости
        </label>
        <label class="modal__label">
          <input type="radio" name="type" value="gaus" v-model="type"  />
          Фильтр Гаусса (3 на 3)
        </label>
        <label class="modal__label">
          <input type="radio" name="type" value="rect" v-model="type"  />
          Прямоугольное размытие
        </label>
      </div>
    </div>
    <div class="modal__line">
      <label>
        <input type="checkbox" v-model="prewiev" @change="applyPrewiev" />
          Предпросмотр
      </label>
      <div class="modal__element">
        <button class="modal__button" @click="filter(), $emit('apply')">Применить</button>
      </div>
      <div class="modal__element">
        <button class="modal__button button-reset" @click="reset">
          <img src="../assets/svg/reset-button.svg" alt="Сброс">
        </button>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: "FiltrationTool",
  props: {
    dx: Number,
    dy: Number,
    nowW: Number,
    nowH: Number,
    ctxRef: CanvasRenderingContext2D,
    startImage: Object,
  },
  data() {
    return {
      prewiev: false,
      type: "same",
      x1: 0,
      x2: 0,
      x3: 0,
      x4: 0,
      x5: 1,
      x6: 0,
      x7: 0,
      x8: 0,
      x9: 0,
      kernel: [
        [0, 0, 0],
        [0, 1, 0],
        [0, 0, 0],
      ],
    };
  },
  methods: {
    changeX1() {
      this.kernel[0][0] = this.x1;
      this.type = "";
      if (this.prewiev){
        this.filter()
      }
    },
    changeX2() {
      this.kernel[0][1] = this.x2;
      this.type = "";
      if (this.prewiev){
        this.filter()
      }
    },
    changeX3() {
      this.kernel[0][2] = this.x3;
      this.type = "";
      if (this.prewiev){
        this.filter()
      }
    },
    changeX4() {
      this.kernel[1][0] = this.x4;
      this.type = "";
      if (this.prewiev){
        this.filter()
      }
    },
    changeX5() {
      this.kernel[1][1] = this.x5;
      this.type = "";
      if (this.prewiev){
        this.filter()
      }
    },
    changeX6() {
      this.kernel[1][2] = this.x6;
      this.type = "";
      if (this.prewiev){
        this.filter()
      }
    },
    changeX7() {
      this.kernel[2][0] = this.x7;
      this.type = "";
      if (this.prewiev){
        this.filter()
      }
    },
    changeX8() {
      this.kernel[2][1] = this.x8;
      this.type = "";
      if (this.prewiev){
        this.filter()
      }
    },
    changeX9() {
      this.kernel[2][2] = this.x9;
      this.type = "";
      if (this.prewiev){
        this.filter()
      }
    },
    reset(){
      this.type = "same"
      if (this.prewiev){
        this.ctxRef.drawImage(
          this.startImage,
          this.dx,
          this.dy,
          this.nowW,
          this.nowH
        );
      }
    },
    applyPrewiev() {
      if (this.prewiev) {
        this.filter();
      } else {
        this.ctxRef.drawImage(
          this.startImage,
          this.dx,
          this.dy,
          this.nowW,
          this.nowH
        );
      }
    },
    filter() {
      this.ctxRef.drawImage(
          this.startImage,
          this.dx,
          this.dy,
          this.nowW,
          this.nowH
        );
      const imageData = this.ctxRef.getImageData(this.dx, this.dy, this.nowW, this.nowH);
      const newData = new Uint8ClampedArray(imageData.data.length);

      const paddedData = this.padImageData(
        imageData.data,
        imageData.width,
        imageData.height
      );

      for (let y = 0; y < imageData.height; y++) {
        for (let x = 0; x < imageData.width; x++) {
          for (let c = 0; c < 4; c++) {
            const outputIndex = (y * imageData.width + x) * 4 + c;
            let sum = 0;
            let kernelSum = 0;
            for (let ky = 0; ky < 3; ky++) {
              for (let kx = 0; kx < 3; kx++) {
                const inputIndex =
                  ((y + ky) * (imageData.width + 2) + (x + kx)) * 4 + c;
                sum += paddedData[inputIndex] * this.kernel[ky][kx];
                kernelSum += this.kernel[ky][kx];
              }
            }
            newData[outputIndex] = sum / kernelSum;
          }
        }
      }

      imageData.data.set(newData);
      this.ctxRef.putImageData(imageData, this.dx, this.dy);
    },

    padImageData(data, width, height) {
      const paddedWidth = width + 2;
      const paddedHeight = height + 2;
      const paddedData = new Uint8ClampedArray(paddedWidth * paddedHeight * 4);

      for (let y = 0; y < height; y++) {
        for (let x = 0; x < width; x++) {
          const inputIndex = (y * width + x) * 4;
          const outputIndex = ((y + 1) * paddedWidth + x + 1) * 4;
          paddedData.set(
            data.subarray(inputIndex, inputIndex + 4),
            outputIndex
          );
        }
      }

      for (let y = 0; y < paddedHeight; y++) {
        for (let x = 0; x < paddedWidth; x++) {
          const outputIndex = (y * paddedWidth + x) * 4;
          if (
            x === 0 ||
            x === paddedWidth - 1 ||
            y === 0 ||
            y === paddedHeight - 1
          ) {
            const nearestX = Math.max(1, Math.min(x, paddedWidth - 2));
            const nearestY = Math.max(1, Math.min(y, paddedHeight - 2));
            const nearestIndex = (nearestY * paddedWidth + nearestX) * 4;
            paddedData.set(
              paddedData.subarray(nearestIndex, nearestIndex + 4),
              outputIndex
            );
          }
        }
      }
      return paddedData;
    },
  },
  watch: {
    type: function (value) {
      if (value == "same") {
        this.kernel = [
          [0, 0, 0],
          [0, 1, 0],
          [0, 0, 0],
        ];
      }
      if (value == "sharp") {
        this.kernel = [
          [-1, -1, -1],
          [-1, 9, -1],
          [-1, -1, -1],
        ];
      }
      if (value == "gaus") {
        this.kernel = [
          [1, 2, 1],
          [2, 4, 2],
          [1, 2, 1],
        ];
      }
      if (value == "rect") {
        this.kernel = [
          [1, 1, 1],
          [1, 1, 1],
          [1, 1, 1],
        ];
      }
      if (this.prewiev){
        this.filter()
      }
    },
    kernel: function (value) {
      this.x1 = value[0][0];
      this.x2 = value[0][1];
      this.x3 = value[0][2];
      this.x4 = value[1][0];
      this.x5 = value[1][1];
      this.x6 = value[1][2];
      this.x7 = value[2][0];
      this.x8 = value[2][1];
      this.x9 = value[2][2];
    },
  },
};
</script>
<style lang="scss" scoped>
.modal {
  position: absolute;
  top: 70px;
  left: 210px;
  background-color: white;
  padding: 15px;
  border-radius: 20px;
  display: flex;
  flex-direction: column;
  row-gap: 16px;

  &__container {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
  }

  &__line {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
  }

  &__line:last-child {
    column-gap: 16px;
  }

  &__element {
    text-align: center;
  }

  &__button{
    border: 1px solid black;
    padding: 4px 8px;
    border-radius: 10px;
  }

  &__label {
    display: flex;
    flex-direction: row;
    column-gap: 20px;
    align-items: center;
  }

  &__input {
    width: 30%;
    border-radius: 5px;
    padding: 3px;
    border: 1px solid black;
  }
}

.type {
  text-align: left;
}

.matrix {
  display: flex;
  flex-direction: column;
  row-gap: 5px;

  &__line {
    display: flex;
    flex-direction: row;
    column-gap: 5px;
    justify-content: center;
  }

  &__input {
    width: 45px;
    padding: 3px;
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

.button-reset {
  background-color: transparent;
  border: none;
  padding: 0;

  & img {
    height: 20px;
  }
}

.cross {
  display: block;
  transform: rotate(45deg);
  line-height: 0;
}

</style>
