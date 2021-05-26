<template>
  <div class="book" draggable="true" @dragover.prevent @drop="dragFinish(book, $event)">
    <!--<p>Title: {{ book.volumeInfo.title }}</p>-->
    <img :src="book.volumeInfo.imageLinks.thumbnail">
    <!--<p>Description: {{ book.volumeInfo.description }}</p>-->
  </div>
</template>
<script>
export default {
  name: "Book",
  props: ["book","compareBookWithDescription"],
  setup(props) {
    function itemDragStart(which, ev) {
      console.log("itemDragStart La descripción:");
      console.log(which);
      console.log(ev);
      // addToExchageArrya(which);
      // props.updateItemsOrder(which,1);
    }

    function dragFinish(to, ev) {
      console.log("dragFinish Libro:");
      console.log(to);
      console.log(ev);
      props.compareBookWithDescription(to.id)
      // addToExchageArrya(to);
      // props.updateItemsOrder(to,2);
    }
    return {
      itemDragStart,
      dragFinish,
    };
  },
};
</script>
<style scoped>
.book{
    border: solid 1px;
}
</style>