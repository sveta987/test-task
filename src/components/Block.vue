<template>
  <div class="bg-[#4472C4] h-[95px] w-[95px] border-[1px] border-black absolute"
       :style="{'top': localTop, 'left': localLeft}"
       ref="draggableContainer"
       @mousedown="dragMouseDown" @dblclick="showDeleteButton">
    <button v-show="isShowDeleteButton"
            class="bg-blue-100 rounded-full border-black border-[1px] absolute top-[-25px] right-[-25px] px-[7px]"
            v-click-outside="onClickOutside" @click="$emit('deleteBlock', block)">X
    </button>
    <div
      class="bg-[#70AD47] h-[30px] w-[30px] rounded-full border-[1px] border-white absolute top-[-15px] left-[30px] cursor-pointer"
      @click="getCoordinate"></div>
    <div
      class="bg-[#70AD47] h-[30px] w-[30px] rounded-full border-[1px] border-white absolute bottom-[-15px] left-[30px] cursor-pointer"
      @click="getCoordinate"></div>
    <div
      class="bg-[#70AD47] h-[30px] w-[30px] rounded-full border-[1px] border-white absolute top-[30px] left-[-15px] cursor-pointer"
      @click="getCoordinate"></div>
    <div
      class="bg-[#70AD47] h-[30px] w-[30px] rounded-full border-[1px] border-white absolute top-[30px] right-[-15px] cursor-pointer"
      @click="getCoordinate"></div>
  </div>
</template>

<script>
import vClickOutside from 'click-outside-vue3'

export default {
  name: "Block",
  props: ['block'],
  directives: {
    clickOutside: vClickOutside.directive
  },
  data() {
    return {
      localTop: null,
      localLeft: null,
      positions: {
        clientX: undefined,
        clientY: undefined,
        movementX: 0,
        movementY: 0
      },
      isShowDeleteButton: false,
      coordinates: {
        x: null,
        y: null,
        relation: null
      }
    }
  },
  methods: {
    dragMouseDown(e) {
      e.preventDefault()
      this.positions.clientX = e.clientX;
      this.positions.clientY = e.clientY;
      document.onmousemove = this.elementDrag;
      document.onmouseup = this.closeDragElement;
    },
    elementDrag(e) {
      e.preventDefault()
      this.positions.movementX = this.positions.clientX - e.clientX;
      this.positions.movementY = this.positions.clientY - e.clientY;
      this.positions.clientX = e.clientX;
      this.positions.clientY = e.clientY;
      this.localTop = (this.$refs.draggableContainer.offsetTop - this.positions.movementY) + 'px';
      this.localLeft = (this.$refs.draggableContainer.offsetLeft - this.positions.movementX) + 'px';

    },
    closeDragElement() {
      document.onmouseup = null;
      document.onmousemove = null;
      let newCoordinates = {
        top: this.localTop,
        left: this.localLeft,
        block: this.block
      };
      this.$emit('changedCoordinates',newCoordinates );
    },
    showDeleteButton() {
      this.isShowDeleteButton = true;
    },
    onClickOutside() {
      this.isShowDeleteButton = false;
    },
    getCoordinate(e) {
      this.coordinates.x = e.clientX;
      this.coordinates.y = e.clientY-70;
      this.coordinates.relation = this.block.count;
      this.$emit('getCoordinate', this.coordinates);
      this.coordinates.x = null;
      this.coordinates.y = null;
      this.coordinates.relation = null;
    }
  },
  mounted() {
    this.localTop = this.block.top + 'px';
    this.localLeft = this.block.left + 'px';
  }
}
</script>