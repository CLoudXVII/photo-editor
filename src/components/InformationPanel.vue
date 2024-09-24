<template>
  <div class="content">
    <p class="font">x: {{ x }}; y: {{ y }}</p>
    <template v-if="showData">
      <div class="params">
        <p class="font">Ширина: {{ width }}px</p> 
        <p class="font">Высота: {{ height }}px</p>
      </div>
      <select class="scale-select" :disabled="state!=''" v-model="scale" @change="$emit('scale')">
        <option value="12">12%</option>
        <option value="25">25%</option>
        <option value="50">50%</option>
        <option value="75">75%</option>
        <option value="100">100%</option>
        <option value="150">150%</option>
        <option value="200">200%</option>
        <option value="250">250%</option>
        <option value="300">300%</option>
      </select>
    </template>
  </div>
</template>
  
  <script>
export default {
  name: "InformationPanel",
  props: {
    x: Number,
    y: Number,
    height: Number,
    width: Number,
    showData: Boolean,
    state: String
  },
  data() {
    return {
      picker: null,
      scale: 100,
    }
  },
  mounted() {
  },
  methods: {
  },
  computed: {
  }
};
</script>
  
<style lang="scss" scoped>
.content {
  width: 200px;
  height: 100%;
  display: flex;
  flex-direction: column;
  row-gap: 20px;
  padding: 10px;
  align-items: flex-start;
}

.font{
  color: black;

  &_center{
    align-self: center;
  }
}

.params{
  display: flex;
  flex-direction: column;
  row-gap: 8px;
}

.scale-select {
  font-size: 14px;
  border-radius: 15%;
  padding: 5px;
  outline: none;
}

</style>
  