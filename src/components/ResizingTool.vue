<template>
  <div class="modal-wrapper">
    <div class="modal">
      <div class="header">
        <p class="header__text">Изменение размера</p>
        <button class="header__button" @click="$emit('show')">
          <span class="cross">+</span>
        </button>
      </div>
      <label>
        Единицы измерения:
        <select v-model="type" @change="chooseType">
          <option value="pixels">В пикселях</option>
          <option value="persentage">В процентах</option>
        </select>
      </label>
      <div class="modal__container">
        <input
          class="input"
          type="number"
          min="1"
          v-model="width"
          @change="updateWidth"
        />
        <span>x</span>
        <input
          class="input"
          type="number"
          min="1"
          v-model="height"
          @change="updateHeight"
        />
      </div>
      <div class="modal__container">
        Разрешение
        <p>До: {{ resNow }} MP</p>
        <p>После: {{ resAfter }} MP</p>
      </div>
      <label>
        Сохранять пропорции:
        <input type="checkbox" v-model="proprtions" />
      </label>
      <label>
        Алгоритм интерполяции
        <select v-model="interpol">
          <option value="nearest">Ближайшие соседи</option>
        </select>
        <ToolTipe
          text="Nearest Neighbor: Each pixel in the new image is assigned the value of the
      nearest pixel in the original image."
        />
      </label>
      <button class="button" @click="$emit('show'), $emit('resize')">
        Отобразить
      </button>
    </div>
  </div>
</template>
      
<script>
import ToolTipe from "./ToolTipe.vue";

export default {
  name: "ResizingTool",
  props: {
    nowW: Number,
    nowH: Number,
  },
  components: {
    ToolTipe,
  },
  data() {
    return {
      type: "pixels",
      width: this.nowW,
      height: this.nowH,
      outputH: null,
      outputW: null,
      proprtions: false,
      interpol: "nearest",
    };
  },
  watch: {
    nowW: function (value) {
      this.width = value;
    },
    nowH: function (value) {
      this.height = value;
    },
  },
  computed: {
    resNow() {
      return Math.round((this.nowH * this.nowW) / 10000) / 100;
    },
    resAfter() {
      return Math.round((this.outputH * this.outputW) / 10000) / 100;
    },
  },
  methods: {
    chooseType() {
      if (this.type === "persentage") {
        this.width = (this.width / this.nowW) * 100;
        this.height = (this.height / this.nowH) * 100;
      } else {
        this.width = (this.width * this.nowW) / 100;
        this.height = (this.height * this.nowH) / 100;
      }
    },
    updateHeight() {
      if (this.proprtions) {
        let coef = this.nowW / this.nowH;
        this.width = Math.round(this.height * coef);
      }
      if (this.type === "persentage") {
        this.outputH = Math.round((this.height * this.nowH) / 100);
        this.outputW = Math.round((this.width * this.nowW) / 100);
      } else {
        this.outputH = this.height;
        this.outputW = this.width;
      }
    },
    updateWidth() {
      if (this.proprtions) {
        let coef = this.nowW / this.nowH;
        this.height = Math.round(this.width * coef);
      }
      if (this.type === "persentage") {
        this.outputH = Math.round((this.height * this.nowH) / 100);
        this.outputW = Math.round((this.width * this.nowW) / 100);
      } else {
        this.outputH = this.height;
        this.outputW = this.width;
      }
    },
  },
};
</script>
      
<style lang="scss" scoped>
label {
  display: flex;
  flex-direction: row;
  align-items: center;
  column-gap: 5px;
}

.modal-wrapper {
  height: 100vh;
  width: calc(100vw - 17px);
  background-color: rgba(0, 0, 0, 0.5);
  position: absolute;
  left: 0;
  top: 0;
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  background-color: white;
  height: 40vh;
  width: 25vw;
  padding: 10px;
  border-radius: 10px;
  border: 1px solid black;
  opacity: 1;

  &__container {
    position: relative;
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
    column-gap: 8px;
  }
}

.header {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  
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
    transform: scale(1.25);
  }
}

.cross {
  display: block;
  transform: rotate(45deg);
  line-height: 0;
}

.button {
  background-color: white;
  border: 1px solid grey;
  border-radius: 10px;
  padding: 10px;
  height: fit-content;
  transition: 0.3s;

  &:hover {
    background-color: black;
    color: white;
  }
}

.input {
  padding-left: 15px;
  height: 20px;
  border-radius: 10px;
  border: 1px solid gray;
  text-align: center;
  font-size: 14px;
  width: 100px;
  outline: none;
}

</style>
      