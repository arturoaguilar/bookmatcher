<template>
  <h3>Puntos: {{ points }}</h3>
  <h2>Time Left: {{ timeLeft }}</h2>
  
  <div  v-show="answered">
      {{ messageScreen }} 
      The right Answer is: 
     <b> {{correctProductTitle}} </b>
     <p> {{correctBookDescription}} </p>
      <img :src="correctProductImage" />
    <button @click="restartBooksAfterAnswer()">Aceptar</button>
  </div>

  <div class="books-container">
    <book
      v-for="book in books"
      :key="book.id"
      :book="book"
      :compareBookWithDescription="compareBookWithDescription">
    </book>
  </div>

  <random-description
    :correctBookId="correctBookId"
    :correctBookDescription="correctBookDescription"
    :itemDragStart="itemDragStart"
  >
  </random-description>

</template>
<script>
import axios from "axios";
import { toRefs, reactive } from "@vue/reactivity";
import Book from "../components/Book.vue";
import { watch } from "@vue/runtime-core";
import RandomDescription from "../components/RandomDescription.vue";
export default {
  components: { Book, RandomDescription },
  name: "Playing",
  setup() {
    const gameState = reactive({
      playing: true,
      timeLeft: 60,
      timeInterval: "",
      finish: false,
      booksNumberChallenge: 2,
      messageScreen: "",
      answered: false
    });

    const booksState = reactive({
      books: [],
      keyWord: "art",
      queryKeywords:['art','terror','car'],
      startIndex: 0,
      maxResults: 40,
    });

    const correctBookState = reactive({
      correctBookId: "",
      correctProductTitle:"",
      correctProductImage:"",
      correctBookDescription: "",
    });

    const playerState = reactive({
      points: 0,
      name: "",
    });

    startTime();


    watch(() => {
      if (gameState.timeLeft==0) {
        stopTime();
        alert('¡¡fin del juego!!');
      }
    });

    function compareBookWithDescription(bookId) {
      correctBookState.correctBookId == bookId ? answer(true) : answer(false);
      gameState.answered= true;
    }

    function answer(type) {
      if (type == true) {
        playerState.points++;
        gameState.messageScreen = "Good Answer";
      } else {
        gameState.messageScreen = "wrong Answer";
      }
    }
    function itemDragStart(which, ev) {
      console.log("itemDragStart La descripción:");
      console.log(which);
      console.log(ev);
    }

    function dragFinish(to, ev) {
      console.log("dragFinish Libro:");
      console.log(to);
      console.log(ev);
    }

    function startTime() {
      console.log("STARTING");
      console.log(gameState.timeInterval);
      getTime();
    }

    function stopTime() {
      console.log("STOP");
      clearInterval(gameState.timeInterval);
    }

    function getTime() {
      gameState.timeInterval = setInterval(() => {
        if (gameState.timeLeft === 0) {
          clearInterval(gameState.timeInterval);
        } else {
          gameState.timeLeft--;
        }
      }, 1000);
    }

    (async () => {
      booksState.books = await getBooks();
      correctBookState.correctBookId = booksState.books[0].id;
      correctBookState.correctBookDescription = booksState.books[0].volumeInfo.description;
      correctBookState.correctProductTitle = booksState.books[0].volumeInfo.title;
      correctBookState.correctProductImage = booksState.books[0].volumeInfo.imageLinks.thumbnail;
    })();

    async function getBooks() {
      try {
        const wordIndex = getRandomInt(0,booksState.queryKeywords.length);
        const word = booksState.queryKeywords[wordIndex];
        //booksState.keyWord   
        const AllBooks = await axios.get(
          `https://www.googleapis.com/books/v1/volumes?q=${word}&startIndex=${booksState.startIndex}&maxResults=${booksState.maxResults}`
        );
        return getRandomElements(
          AllBooks.data.items.filter(haveImageAndDescription),
          2
        );
      } catch (error) {
        console.error(error);
      }
    }
    
    function getRandomInt(min, max) {
      return Math.floor(Math.random() * (max - min)) + min;
    }

    function haveImageAndDescription(book) {
      if (
        "imageLinks" in book.volumeInfo &&
        "description" in book.volumeInfo &&
        book.volumeInfo.language === "en"
      ) {
        return true;
      } else {
        return false;
      }
    }

function restartBooksAfterAnswer(){
    (async () => {
      booksState.books = await getBooks();
      correctBookState.correctBookId = booksState.books[0].id;
      correctBookState.correctBookDescription = booksState.books[0].volumeInfo.description;
      correctBookState.correctProductTitle = booksState.books[0].volumeInfo.title;
      correctBookState.correctProductImage = booksState.books[0].volumeInfo.imageLinks.thumbnail;
    })();
    gameState.answered=false;
}

    function getRandomElements(array, numberOfElements) {
      const randomElements = [];

      for (let index = 0; index < numberOfElements; index++) {
        console.log("random");
        console.log(array[Math.floor(Math.random() * numberOfElements)]);
        randomElements.push(
          array[Math.floor(Math.random() * numberOfElements)]
        );
      }
      return randomElements;
    }

    return {
      compareBookWithDescription,
      ...toRefs(booksState),
      ...toRefs(gameState),
      ...toRefs(playerState),
      ...toRefs(correctBookState),
      restartBooksAfterAnswer,
      dragFinish,
      itemDragStart,
    };
  },
};
</script>
<style scoped>
.books-container {
  display: flex;
  flex-direction: row;
  justify-content: center;
  gap: 50px;
}
</style>