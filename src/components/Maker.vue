<template>
  <div class="hello">
    <h1 class="text-center font-bold text-2xl text-white bg-gray-500">Maker</h1>
    <div class="flex">
      <div>
        <canvas
          class="border-2 m-2"
          ref="can"
          width="800"
          height="800"
        ></canvas>
      </div>
      <div
        id="controls"
        class="bg-gray-200 w-full ml-2 grid items-center justify-center m-2"
      >
        <v-button id="addNewSquare" :onClick="addNewSquare"
          >Add New Square</v-button
        >
        <v-button id="evenlySpaceVertically" :onClick="evenlySpaceVertically"
          >Evenly Space Vertically</v-button
        >
        <v-button id="getCurrentObjects" :onClick="getCurrentCanvasObjects"
          >Console Log Current Objects</v-button
        >
        <v-button id="reRenderCanvas" :onClick="reRenderCanvas"
          >Re-Render Canvas</v-button
        >
        <div>
          <label for="colour">Change Colour</label>
          <select
            id="colour"
            @change="updateColour($event)"
            v-model="currentObjectColour"
            :disabled="this.canvasActiveObjects.length == 0"
          >
            <option disabled value="">Please Select</option>
            <option
              v-for="colour in colours"
              :value="colour"
              v-bind:key="colour"
            >
              {{ capitalineFirstLetter(colour) }}
            </option>
          </select>
        </div>
        <div class="flex flex-row justify-evenly">
          <v-button
            id="reRenderCanvas"
            class="w-1/2 mr-2"
            :onClick="reRenderCanvas"
            >Undo</v-button
          >
          <v-button
            id="reRenderCanvas"
            class="w-1/2"
            :onClick="reRenderCanvas"
            v-bind:disable="redoButtonDisabled"
            >Redo</v-button
          >
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { fabric } from "fabric";
import Button from "./Button";

//Shapes
const rect = {
  fill: "red",
  width: 100,
  height: 100,
  left: 0,
  top: 0,
};

export default {
  name: "Maker",
  data: () => ({
    canvas: null,
    canvasActiveObjects: [],
    redoButtonDisabled: true,
    currentObjectColour: "",
    colours: ["red", "green", "blue", "yellow", "pink", "purple"],
  }),
  mounted() {
    const ref = this.$refs.can;
    this.canvas = new fabric.Canvas(ref);
    this.addNewSquare();

    this.canvas.on({
      "object:modified": this.canvasSaveState,
      "object:added": this.canvasSaveState,
      "selection:created": this.canvasHandleSelection,
      "selection:updated": this.canvasHandleSelection,
      "selection:cleared": this.canvasHandleSelectionCleared,
    });
  },
  methods: {
    canvasSaveState() {
      console.log("Modified");
    },
    canvasHandleSelection(e) {
      this.canvasActiveObjects = e.selected;
      if (this.canvasActiveObjects.length == 1)
        this.currentObjectColour = e.selected[0].fill;
    },
    canvasHandleSelectionCleared() {
      this.canvasActiveObjects = [];
      this.currentObjectColour = "";
    },
    addNewSquare() {
      this.canvas.add(new fabric.Rect(rect));
    },
    evenlySpaceVertically() {
      let objects = this.canvas.getObjects();
      let leftOffset = 0;
      let topOffset = 0;
      let colMaxWidth = 0;
      let padding = 10;
      objects.forEach((obj, index) => {
        const objWidth = obj.getScaledWidth();
        const objHeight = obj.getScaledHeight();
        obj.set("left", leftOffset);
        obj.set("top", topOffset);
        obj.setCoords();
        topOffset += objHeight + padding;

        if (objWidth > colMaxWidth) colMaxWidth = objWidth;

        // Check if the next object will fit under the current object
        if (objects[index + 1] != null) {
          if (
            topOffset + objects[index + 1].getScaledHeight() >
            this.canvas.height
          ) {
            // If object can't fit then move to next row
            topOffset = 0;
            leftOffset += colMaxWidth + padding;
            colMaxWidth = 0;
          }
        }
      });
      this.reRenderCanvas();
    },
    reRenderCanvas() {
      this.canvas.renderAll();
      this.canvasSaveState();
    },
    getCurrentCanvasObjects() {
      console.log(this.canvas.getObjects());
      return this.canvas.getObjects();
    },
    updateColour(event) {
      this.canvasActiveObjects.forEach((obj) => {
        obj.set("fill", event.target.value);
      });
      this.reRenderCanvas();
    },
    capitalineFirstLetter(str) {
      return str ? str[0].toUpperCase() + str.slice(1) : "";
    },
  },
  components: {
    "v-button": Button,
  },
};
</script>

<style scoped>
#controls {
  height: 800px;
}
label {
  --tw-bg-opacity: 1;
  background-color: rgba(107, 114, 128, var(--tw-bg-opacity));
  color: white;
  height: 45px;
}
label,
select {
  padding: 0.876rem;
}
</style>
