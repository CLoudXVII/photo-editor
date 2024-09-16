<template>
  <div class="photo-editor">
    <h1 class="main-title">WebPE</h1>
    <ToolPanel
      @show="changeImageUploadTool"
      @clear="clear(), closeDisplay(), updateCanvas()"
      @scale="changeResizingTool"
      @Pippete="changePippete"
      @grab="changeGrab"
      @curve="changeCurves"
      @filtering="changeFiltering"
      @save="saveImage"
      :state="this.state"
      :startImage="this.startImage"
      class="header"
      ref="header"
    />
    <InformationPanel
      class="sidebar"
      @scale="scaleImage()"
      :x="this.x"
      :state="this.state"
      :y="this.y"
      :height="this.height"
      :width="this.width"
      :resl="this.resl"
      :showData="this.showData"
      ref="sidebar"
    />
    <div class="canvas-container">
      <canvas
        width="1514"
        height="835"
        ref="canvas-container"
        @mousedown="handleMouseDown"
        @mouseup="handleMouseUp"
        @mousemove="handleMouseMove"
        @mousewheel="handleMouseWheel"
      />
    </div>
    <PippeteTool
      @show="changePippete"
      :resl="this.resl"
      :startX="this.startX"
      :startY="this.startY"
      :nowW="this.nowW"
      :nowH="this.nowH"
      ref="Pippete"
      v-if="showPippeteTool"
    />
    <CurvesTool
      @show="changeCurves"
      @apply="changeCurves(), updateImage()"
      :ctxRef="this.ctx"
      :dx="this.dx"
      :dy="this.dy"
      :nowW="this.nowW"
      :nowH="this.nowH"
      :startImage="this.startImage"
      :showCurvesTool="this.showCurvesTool"
      ref="curves"
      v-show="showCurvesTool"
    />
    <FiltrationTool
      @show="changeFiltering"
      @apply="changeFiltering(), updateImage()"
      :ctxRef="this.ctx"
      :dx="this.dx"
      :dy="this.dy"
      :nowW="this.nowW"
      :nowH="this.nowH"
      :startImage="this.startImage"
      ref="Filtering"
      v-if="showFilterModal"
    />
    <div style="display: none">
      <canvas ref="canvasHelper" />
    </div>
    <ImageUploadTool
      v-show="showImageUploadTool"
      @show="changeImageUploadTool"
      @download="clear(), draw(), showDisplay()"
      ref="modal"
    />
    <ResizingTool
      :nowH="this.nowH"
      :nowW="this.nowW"
      v-show="showResizingTool"
      @show="changeResizingTool"
      ref="ResizingTool"
      @resize="resize"
    />
  </div>
</template>

<script>
import ToolPanel from "./ToolPanel.vue";
import InformationPanel from "./InformationPanel.vue";
import ImageUploadTool from "./ImageUploadTool.vue";
import ResizingTool from "./ResizingTool.vue";
import PippeteTool from "./PippeteTool.vue";
import CurvesTool from "./CurvesTool.vue";
import FiltrationTool from "./FiltrationTool.vue";
export default {
  name: "PhotoEditor",
  components: {
    InformationPanel,
    ToolPanel,
    ImageUploadTool,
    ResizingTool,
    PippeteTool,
    CurvesTool,
    FiltrationTool,
  },
  data() {
    return {
      startImage: null,
      canvas: null,
      ctx: null,
      canvasHelper: null,
      ctxHelper: null,
      resl: null,
      x: 0,
      y: 0,
      showImageUploadTool: false,
      showResizingTool: false,
      showPippeteTool: false,
      showCurvesTool: false,
      showFilterModal: false,
      showData: false,
      height: 0,
      width: 0,
      nowH: null,
      nowW: null,
      dx: null,
      dy: null,
      isDragging: false,
      startX: null,
      startY: null,
      state: "",
      isShift: false,
    };
  },
  mounted() {
    this.canvas = this.$refs["canvas-container"];
    this.ctx = this.canvas.getContext("2d", { willReadFrequently: true });
    this.canvasHelper = this.$refs["canvasHelper"];
    this.ctxHelper = this.canvasHelper.getContext("2d", {
      willReadFrequently: true,
    });
    window.addEventListener("keydown", this.handlePressShiftKey);
    window.addEventListener("keyup", this.handleUnpressShiftKey);
  },
  methods: {
    updateCanvas(){
      this.$refs.modal.result = null;
      this.startImage = null;
    },
    handleMouseDown(e) {
      this.isDragging = true;
      this.startX = e.offsetX - this.dx;
      this.startY = e.offsetY - this.dy;
      if (this.state == "Pippete" && this.$refs.modal.result != null) {
        this.resl = this.ctx.getImageData(this.x, this.y, 1, 1).data;
      }
    },
    handleMouseMove(e) {
      this.x = e.offsetX;
      this.y = e.offsetY;
      if (this.isDragging && this.state == "grab") {
        this.dx = e.offsetX - this.startX;
        this.dy = e.offsetY - this.startY;
        if (this.dx + 20 > this.canvas.width) {
          this.dx = this.canvas.width - 20;
        }
        if (this.dy + 20 > this.canvas.height) {
          this.dy = this.canvas.height - 20;
        }
        if (this.dx + this.nowW < 20) {
          this.dx = -this.nowW + 20;
        }
        if (this.dy + this.nowH < 20) {
          this.dy = -this.nowH + 20;
        }
        this.moveImage();
      }
    },
    changeGrab() {
      if (this.state == "grab") {
        this.state = "";
      } else {
        this.state = "grab";
      }
      this.showPippeteTool = false;
      this.showCurvesTool = false;
      this.showFilterModal = false;
    },
    changePippete() {
      if (this.state == "Pippete") {
        this.state = "";
      } else {
        this.state = "Pippete";
      }
      this.showPippeteTool = !this.showPippeteTool;
      this.showCurvesTool = false;
      this.showFilterModal = false;
    },
    changeCurves() {
      if (this.state == "curves") {
        this.state = "";
      } else {
        this.state = "curves";
      }
      this.showCurvesTool = !this.showCurvesTool;
      this.showPippeteTool = false;
      this.showFilterModal = false;
    },
    changeFiltering() {
      if (this.state == "filter") {
        this.state = "";
      } else {
        this.state = "filter";
      }
      this.showPippeteTool = false;
      this.showCurvesTool = false;
      this.showFilterModal = !this.showFilterModal;
    },
    handleMouseWheel(event) {
      if (
        this.state != "Pippete" &&
        this.startImage != null &&
        this.state != "curves" &&
        this.state != "filter"
      ) {
        event.preventDefault();
        const delta = Math.sign(event.deltaY);

        if (this.isShift) {
          this.dx -= delta * 6;
        } else {
          this.dy += delta * 6;
        }
        if (this.dx + 20 > this.canvas.width) {
          this.dx = this.canvas.width - 20;
        }
        if (this.dy + 20 > this.canvas.height) {
          this.dy = this.canvas.height - 20;
        }
        if (this.dx + this.nowW < 20) {
          this.dx = -this.nowW + 20;
        }
        if (this.dy + this.nowH < 20) {
          this.dy = -this.nowH + 20;
        }
        this.moveImage();
      }
    },
    handlePressShiftKey(event) {
      if (event.key === "Shift") {
        this.isShift = true;
      }
    },
    handleUnpressShiftKey(event) {
      if (event.key === "Shift") {
        this.isShift = false;
      }
    },
    handleMouseUp() {
      this.isDragging = false;
    },
    moveImage() {
      this.clear();
      this.ctx.drawImage(
        this.$refs.modal.result,
        this.dx,
        this.dy,
        this.nowW,
        this.nowH
      );
    },
    draw() {
      this.startImage = this.$refs.modal.result;
      this.height = this.$refs.modal.result.height;
      this.width = this.$refs.modal.result.width;
      if (
        this.height > this.canvas.height - 100 ||
        this.width > this.canvas.width - 100
      ) {
        let scale_factor = Math.min(
          this.canvas.width / this.width,
          this.canvas.height / this.height
        );
        let newWidth = this.width * scale_factor - 100;
        let newHeight = this.height * scale_factor - 100;
        this.nowH = Math.round(newHeight);
        this.nowW = Math.round(newWidth);
        let x = this.canvas.width / 2 - newWidth / 2;
        let y = this.canvas.height / 2 - newHeight / 2;
        this.dx = Math.round(x);
        this.dy = Math.round(y);
        this.ctx.drawImage(this.$refs.modal.result, x, y, newWidth, newHeight);
      } else {
        let x = this.canvas.width / 2 - this.height / 2;
        let y = this.canvas.height / 2 - this.width / 2;
        this.dx = Math.round(x);
        this.dy = Math.round(y);
        this.nowH = Math.round(this.height);
        this.nowW = Math.round(this.width);
        this.ctx.drawImage(
          this.$refs.modal.result,
          x,
          y,
          this.width,
          this.height
        );
      }
    },
    changeImageUploadTool() {
      this.showImageUploadTool = !this.showImageUploadTool;
    },
    changeResizingTool() {
      this.showResizingTool = !this.showResizingTool;
    },
    updateImage() {
      let newImage = this.ctx.getImageData(
        this.dx,
        this.dy,
        this.nowW,
        this.nowH
      );
      this.canvasHelper.width = this.nowW;
      this.canvasHelper.height = this.nowH;
      this.ctxHelper.putImageData(newImage, 0, 0);
      let image = new Image();
      image.src = this.canvasHelper.toDataURL();
      this.$refs.modal.result = image;
      this.startImage = image;
    },
    showDisplay() {
      this.showData = true;
    },
    closeDisplay() {
      this.showData = false;
    },
    clear() {
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
      this.resl = null;
    },
    saveImage() {
      this.canvasHelper.width = this.width;
      this.canvasHelper.height = this.height;
      this.ctxHelper.drawImage(
        this.$refs.modal.result,
        0,
        0,
        this.width,
        this.height
      );
      const imageDataURL = this.canvasHelper.toDataURL("image/png");
      const link = document.createElement("a");
      link.href = imageDataURL;
      link.download = "my_image.png";
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    },
    resize() {
      let dx = this.canvas.width / 2 - this.nowW / 2;
      let dy = this.canvas.height / 2 - this.nowH / 2;
      const img = this.ctx.getImageData(dx, dy, this.nowW, this.nowH);
      const originalWidth = this.nowW;
      const originalHeight = this.nowH;
      const newHeight = this.$refs.ResizingTool.outputH;
      const newWidth = this.$refs.ResizingTool.outputW;
      const scaleX = originalWidth / newWidth;
      const scaleY = originalHeight / newHeight;
      const newData = new Uint8ClampedArray(newWidth * newHeight * 4);

      for (let y = 0; y < newHeight; y++) {
        for (let x = 0; x < newWidth; x++) {
          const px = Math.floor(x * scaleX);
          const py = Math.floor(y * scaleY);
          const index = (y * newWidth + x) * 4;
          const originalIndex = (py * originalWidth + px) * 4;

          newData[index] = img.data[originalIndex];
          newData[index + 1] = img.data[originalIndex + 1];
          newData[index + 2] = img.data[originalIndex + 2];
          newData[index + 3] = img.data[originalIndex + 3];
        }
      }
      let nx = this.canvas.width / 2 - newWidth / 2;
      let ny = this.canvas.height / 2 - newHeight / 2;
      let image = new ImageData(newData, newWidth, newHeight);
      this.clear();
      this.ctx.putImageData(image, nx, ny);
      this.height = newHeight;
      this.width = newWidth;
      this.nowH = newHeight;
      this.nowW = newWidth;
      this.dx = Math.round(nx);
      this.dy = Math.round(ny);
    },
    scaleImage() {
      let scalePers = this.$refs.sidebar.scale / 100;
      if (
        this.height > this.canvas.height - 100 ||
        this.width > this.canvas.width - 100
      ) {
        let scale_factor = Math.min(
          this.canvas.width / this.width,
          this.canvas.height / this.height
        );
        let newWidth = (this.width * scale_factor - 100) * scalePers;
        let newHeight = (this.height * scale_factor - 100) * scalePers;
        this.nowH = Math.round(newHeight);
        this.nowW = Math.round(newWidth);
        let x = this.canvas.width / 2 - newWidth / 2;
        let y = this.canvas.height / 2 - newHeight / 2;
        this.dx = Math.round(x);
        this.dy = Math.round(y);
        this.clear();
        this.ctx.drawImage(this.$refs.modal.result, x, y, newWidth, newHeight);
      } else {
        let newWidth = this.width * scalePers;
        let newHeight = this.height * scalePers;
        this.nowH = Math.round(newHeight);
        this.nowW = Math.round(newWidth);
        let x = this.canvas.width / 2 - newHeight / 2;
        let y = this.canvas.height / 2 - newWidth / 2;
        this.dx = Math.round(x);
        this.dy = Math.round(y);
        this.clear();
        this.ctx.drawImage(this.$refs.modal.result, x, y, newWidth, newHeight);
      }
    },
  },
  computed: {},
};
</script>

<style lang="scss" scoped>
.photo-editor {
  height: calc(100% - 3px);
  display: grid;
  grid-template-columns: 200px auto 200px;
  grid-template-rows: 60px auto 80px;
}

.main-title {
  font-size: 45px;
  color: black;
  font-weight: bold;
  grid-column: 2 / 3;
  grid-row: 1 / 2;
  align-self: center;
  justify-self: center;
}

.header {
  grid-row: 3 / 4;
  grid-column: 2 / 3;
  align-self: center;
}

.sidebar {
  grid-row: 2 / 3;
  grid-column: 1 / 2;
}

.source {
  display: none;
}

.canvas-container {
  grid-row: 2 / 3;
  grid-column: 2 / 3;
  inline-size: 100%;
  block-size: 100%;
  background-color: #2f2f2f;
  background-size: 20%;
  background-repeat: repeat;
  border: 1px solid gray;
}

</style>
