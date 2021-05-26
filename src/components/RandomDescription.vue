<template>
  <div class="descripton"  draggable="true" @dragstart="itemDragStart(correctBookId, $event)"
    @dragover.prevent
     style="border:solid 1px black">
    {{ correctBookDescription }}
  </div>
</template>
<script>
export default {
  props: ["correctBookDescription", "itemDragStart","correctBookId"],
  setup() {

  },
};
</script>
