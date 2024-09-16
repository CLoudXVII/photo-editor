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
      <div v-if="resl != null" class="color">
          <div class="color__img" :style="string"></div>
          <div class="color__inf">
            <p class="color__font">{{ color }}</p>
            <p class="color__font">Hex: {{ HEX }}</p>
          </div>
        </div>
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
    resl: Array,
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
    string() {
      return (
        "background-color: rgb(" +
        this.resl[0] +
        "," +
        this.resl[1] +
        "," +
        this.resl[2] +
        ");"
      );
    },
    color() {
      return (
        "rgb(" + this.resl[0] + ", " + this.resl[1] + ", " + this.resl[2] + ")"
      );
    },
    HEX() {
      return (
        "#" +
        [this.resl[0], this.resl[1], this.resl[2]]
          .map((x) => {
            const hex = x.toString(16);
            return hex.length === 1 ? "0" + hex : hex;
          })
          .join("")
      );
    },
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

.color {
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  row-gap: 16px;
  margin-top: 10px;
  align-items: center;

  &__img{
  height: 35px;
  width: 35px;
  border-radius: 50%;
  border: 1px solid gray;
  }

  &__font{
    color: black;
  }

  &__inf{
    display: flex;
    flex-direction: column;
    align-items: center;
    row-gap: 10px;
  }
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
  