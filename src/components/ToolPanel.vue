<template>
  <nav class="navigation">
    <ul class="tool-panel">
      <li class="tool-panel__item">
        <button class="button" @click="$emit('show')" title="Загрузить изображение...">
          <img class="button__img" src="../assets/svg/add-image.svg" alt="">
        </button>
      </li>
      <li class="tool-panel__item">
        <button class="button" :disabled="!startImage" :class="{ active: isGrab}" @click="$emit('grab')" title="Перетащить изображение">
          <img class="button__img" src="../assets/svg/grab-image.svg" alt="">
        </button>
      </li>
      <li class="tool-panel__item">
        <button class="button" :disabled="!startImage" :class="{ active: isPippete}" @click="$emit('Pippete')" title='Пипетка (для выбора дополнительного цвета зажмите клавишу "Alt")'>
          <img class="button__img" src="../assets/svg/pipette.svg" alt="">
        </button>
      </li>
      <li class="tool-panel__item">
        <button class="button" :disabled="!startImage" @click="$emit('scale')" title="Изменить размер изображения">
          <img class="button__img" src="../assets/svg/resize-image.svg" alt="">
        </button>
      </li>
      <li class="tool-panel__item">
        <button class="button" :disabled="!startImage" :class="{ active: isCurves}" @click="$emit('curve')" title="Кривые">
          <img class="button__img" src="../assets/svg/graph.svg" alt="">
        </button>
      </li>
      <li class="tool-panel__item">
        <button class="button" :disabled="!startImage" :class="{ active: isFiltering}" @click="$emit('filtering')" title="Изменить фильтры изображения">
          <img class="button__img" src="../assets/svg/filter-image.svg" alt="">
        </button>
      </li>
      <li class="tool-panel__item">
        <button class="button" :disabled="!startImage" @click="$emit('save')" title="Сохранить изображение">
          <img class="button__img" src="../assets/svg/save-image.svg" alt="">
        </button>
      </li>
      <li class="tool-panel__item">
        <button class="button" :disabled="!startImage" @click="$emit('clear')" title="Очистить холст">
          <img class="button__img" src="../assets/svg/delete-image.svg" alt="">
        </button>
      </li>
    </ul>
  </nav>
</template>
  
  <script>
export default {
  name: "ToolPanel",
  props: {
    state: String,
    startImage: Object,
  },
  data() {
    return {
      new: null,
    };
  },

  methods: {
  },
  computed:{
    isPippete(){
      if (this.state == 'Pippete'){
        return true
      } else {
        return false
      }
    },
    isGrab(){
      if (this.state == 'grab'){
        return true
      } else {
        return false
      }
    },
    isCurves(){
      if (this.state == 'curves'){
        return true
      } else {
        return false
      }
    },
    isFiltering(){
      if (this.state == 'filter'){
        return true
      } else {
        return false
      }
    }
  }
};
</script>
  
<style lang="scss" scoped>
.button{
  background-color: white;
  border: 1px solid black;
  border-radius: 10px;
  padding: 7px;
  height: fit-content;
  transition: 0.2s;

  &__img{
    height: 35px;
  }

  &:disabled{
    background-color: rgb(122, 122, 122);
  }

  &.active{
    background-color: rgb(122, 122, 122);
  }

  &:not(:disabled):hover {
    background-color: rgb(197, 197, 197);
  }
}

.tool-panel {
  display: grid;
  grid-auto-flow: column;
  grid-auto-columns: auto;

  &__item {
    justify-self: center;
  }

  &__item:first-child {
    justify-self: left;
  }

  &__item:last-child {
    justify-self: right;
  }
}
</style>
  