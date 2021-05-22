<template>
  <book v-for="book in books" :key="book.id" :book="book"></book>
</template>
<script>
import axios from "axios";
import { ref, toRefs, reactive } from "@vue/reactivity";
import Book from "../components/Book.vue";
export default {
  components: { Book },
  setup() {
    const instructions = ref("Lets play");
    const booksState = reactive({
      books: [],
      keyWord: "art",
      startIndex: 0,
      maxResults: 40,
    });

    (async () => {
      booksState.books = await getBooks();
    })();

    async function getBooks() {
      try {
        const AllBooks = await axios.get(
          `https://www.googleapis.com/books/v1/volumes?q=${booksState.keyWord}&startIndex=${booksState.startIndex}&maxResults=${booksState.maxResults}`
        );

        return getRandomElements(AllBooks.data.items.filter(haveImageAndDescription),2);
      } catch (error) {
        console.error(error);
      }
    }

    
     function haveImageAndDescription(book) {

      if ("imageLinks" in book.volumeInfo && "description" in book.volumeInfo &&  book.volumeInfo.language === "en" )  {
        return true;
      } else {
        return false;
      }
    }

    function getRandomElements(array,numberOfElements){
        const randomElements =[];
       
        for (let index = 0; index < numberOfElements; index++) {
           // const element = array[index];
           //Math.floor(Math.random() * numberOfElements);
           console.log("random");
           console.log(array[Math.floor(Math.random() * numberOfElements)]);
           randomElements.push(array[Math.floor(Math.random() * numberOfElements)]);
            
        }
        return randomElements;
    }

   /* function filtrarPorID(obj) {
  if ('id' in obj && typeof(obj.id) === 'number' && !isNaN(obj.id)) {
    return true;
  } else {
    entradasInvalidas++;
    return false;
  }
} */

    return {
      instructions,
      booksState,
      ...toRefs(booksState),
    };
  },
};
</script>