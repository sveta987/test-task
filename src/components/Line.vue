<template>
    <line :x1="localCoordinates?.firstNodeX"
          :y1="localCoordinates?.firstNodeY"
          :x2="localCoordinates?.secondNodeX"
          :y2="localCoordinates?.secondNodeY"
          v-show="localCoordinates"
          class="stroke-[#4472C4] stroke-2 z-50"/>
</template>

<script>
export default {
  name: "Line2",
  props:['line'],
  data(){
    return{
      localCoordinates:null,
      isShowDeleteButton: false
    }
  },
  mounted() {
    this.localCoordinates =this.line;
    this.$emit('lineIsReady');
  }
}
</script>