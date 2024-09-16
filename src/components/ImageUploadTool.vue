<template>
  <div class="modal-wrapper">
    <dialog class="modal">
      <div class="modal__header header">
        <p class="header__text">Выбор изображения</p>
        <button class="header__button" @click="$emit('show')">
          <span class="cross">
            +
          </span>
        </button>
      </div>
      <div class="image-input">
        <label>
          Ссылка:
          <input class="input" type="text" @change="savenewUrl" v-model="url" />
        </label>
        <label>
          Выбрать файл:
          <input
            class="input"
            type="file"
            ref="inputFile"
            @change="savenewFile"
          />
        </label>
      </div>
      <button
        class="button"
        @click="saveResult(), $emit('show'), $emit('download')"
      >
        Подтвердить
      </button>
    </dialog>
  </div>
</template>
    
<script>
export default {
  name: "ImageUploadTool",
  data() {
    return {
      Url: null,
      File: null,
      url: null,
      result: null,
    };
  },
  mounted() {
  },
  methods: {
    async savenewUrl() {
      fetch(this.url)
        .then((response) => response.blob())
        .then((blob) => {
          this.File = new Image();
          this.File.src = URL.createObjectURL(blob);
        });
    },
    savenewFile(e) {
      let img = e.target.files[0];
      this.File = new Image();
      this.File.src = URL.createObjectURL(img);
    },
    saveResult() {
      if (this.Url != null && this.File != null) {
        this.result = this.File;
      } else if (this.Url != null && this.File == null) {
        this.result = this.Url;
      } else if (this.Url == null && this.File != null) {
        this.result = this.File;
      }
      this.Url = null;
      this.url = null;
      this.$refs.inputFile.value = null;
      this.File = null;
    },
  },
};
</script>
    
<style lang="scss" scoped>
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
  width: 25vw;
  height: 25vh;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  background-color: white;
  padding: 16px;
  border-radius: 10px;
  border: 1px solid gray;

  &__header {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
  }
}

.header {
  &__text {
    font-size: 20px;
  }
  &__button {
    background: none;
    border: none;
    font-size: 50px;
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

.image-input {
  display: flex;
  flex-direction: column;
  row-gap: 24px;
}

.button {
  background-color: white;
  border: 1px solid gray;
  border-radius: 10px;
  padding: 10px;
  height: fit-content;
  transition: 0.3s;
}

.button:hover {
  background-color: black;
  color: white;
}

.input {
  height: 20px;
  border-radius: 5px;
  outline: none;
}
</style>
    