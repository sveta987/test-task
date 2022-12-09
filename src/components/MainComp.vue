<template>
  <button @click="addBlock">add block</button>

  <div v-for="block in blocks" :key="block.count">
    <block :block="block" @deleteBlock="deleteBlock" @getCoordinate="getCoordinate" @changedCoordinates="changedCoordinates"/>

  </div>
  <button v-show="isShowDeleteButton"
          class="bg-blue-100 rounded-full border-black border-[1px] absolute px-[7px]"
          :style="{'top': coordinates.secondNodeY+50 + 'px','left': coordinates.secondNodeX-50 +'px'}"
          v-click-outside="onClickOutside" @click="deleteLine"> X
  </button>
  <svg height="500" width="1000">
    <Line :line="line" @lineIsReady="lineIsReady" v-for="line in lines" :key="line.count"
           @dblclick="showDeleteButton(line)"/>
  </svg>
</template>

<script>
import Block from "@/components/Block";
import Line from "@/components/Line";
import vClickOutside from "click-outside-vue3";

export default {
  name: "MainComp",
  components: {Block, Line},
  directives: {
    clickOutside: vClickOutside.directive
  },
  data() {
    return {
      count: 0,
      lineCount: 0,
      blocks: [],
      lines: [],
      isShowDeleteButton: true,
      indexOfLine: null,
      coordinates: {
        firstNodeX: null,
        firstNodeY: null,
        secondNodeX: null,
        secondNodeY: null,
        firstRelation: null,
        secondRelation: null,
      }
    }
  },
  methods: {
    addBlock() {
      this.count++;
      let newBlock = {};
      newBlock.top = Math.floor(Math.random() * (700) + 1);
      newBlock.left = Math.floor(Math.random() * (700) + 1);
      newBlock.count = this.count;
      this.blocks.push(newBlock);
    },
    changedCoordinates(value){
      let newBlock = value.block;
      newBlock.top = value.top;
      newBlock.left = value.left;
      this.blocks.splice(this.blocks.indexOf(value.block),1, newBlock);
      this.lines.forEach((line, index) => {
        if(line.firstRelation === newBlock.count){
          this.lines[index].firstNodeX = newBlock.left ;
          this.lines[index].firstNodeY = newBlock.top ;
        }
        else{
          if(line.secondRelation ===newBlock.count){
            this.lines[index].secondNodeX = newBlock.left ;
            this.lines[index].secondNodeY = newBlock.top ;
          }
        }
      })
    },
    deleteBlock(value) {
      this.blocks.splice(this.blocks.indexOf(value), 1);
      let deletedBlock = value;
      this.lines = this.lines.filter(line => line.firstRelation !== deletedBlock.count && line.secondRelation !== deletedBlock.count);
    },
    getCoordinate(value) {
      if (!this.coordinates.firstNodeX) {
        this.coordinates.firstNodeX = value.x;
        this.coordinates.firstNodeY = value.y;
        this.coordinates.firstRelation = value.relation;

      } else {
        this.coordinates.secondNodeX = value.x;
        this.coordinates.secondNodeY = value.y;
        this.coordinates.secondRelation = value.relation;
        let newLine = {};
        newLine.firstNodeX = this.coordinates.firstNodeX;
        newLine.firstNodeY = this.coordinates.firstNodeY;
        newLine.secondNodeX = this.coordinates.secondNodeX;
        newLine.secondNodeY = this.coordinates.secondNodeY;
        newLine.firstRelation = this.coordinates.firstRelation;
        newLine.secondRelation = this.coordinates.secondRelation;
        newLine.count = this.lineCount;
        this.lines.push(newLine);
        this.lineCount++;
      }
    },
    lineIsReady() {
      this.coordinates = {};
    },
    showDeleteButton(i) {
      this.isShowDeleteButton = true;
      this.indexOfLine = i;
    },
    onClickOutside(event) {
      this.isShowDeleteButton = false;
    },
    deleteLine() {
      this.lines.splice(this.lines.indexOf(this.indexOfLine), 1);
      this.isShowDeleteButton = false;
    }
  }
}
</script>