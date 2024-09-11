<template>
  <nav class="navigation">
    <ul class="tool-panel">
      <li class="tool-panel__item">
        <button class="button" @click="$emit('show')" title="Загрузить изображение...">
          <img class="button__img" src="../assets/svg/add-image.svg" alt="">
        </button>
      </li>
      <li class="tool-panel__item">
        <button class="button" :disabled="!startImage" :class="{ active: isPippet}" @click="$emit('pippet')" title='Пипетка (для выбора дополнительного цвета зажмите клавишу "Alt")'>
          <img class="button__img" src="../assets/svg/pipette.svg" alt="">
        </button>
      </li>
      <li class="tool-panel__item">
        <button class="button" :disabled="!startImage" :class="{ active: isGrab}" @click="$emit('grab')" title="Перетащить изображение">
          <img class="button__img" src="../assets/svg/grab-image.svg" alt="">
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
  name: "HeaderPanel",
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
    isPippet(){
      if (this.state == 'pippet'){
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
  margin-left: 20px;

  &__img{
    height: 35px;
  }

  &:disabled{
    background-color: rgb(122, 122, 122);
  }

  &.active{
    background-color: rgb(122, 122, 122);
  }
}

.tool-panel {
  height: 80px;
  display: flex;
  flex-direction: row;
  align-items: center;
  column-gap: 50px;
  padding: 10px;

  &__item:last-child {
    margin-left: auto;
    margin-right: 20px;
  }

}
</style>
  