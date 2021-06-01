<template>
  <div class="playing">
    <!-- modal -->
    <div id="endModal" class="modal">
      <div class="modal-background"></div>
      <div class="modal-content">
        <end :name="name" :points="points"> </end>
        <!-- Any other Bulma elements you want -->
      </div>
      <button
        @click="closeEndModal()"
        class="modal-close is-large"
        aria-label="close"
      ></button>
    </div>
    <!-- -->
    <div class="dashboard">
      <p>Go {{ name }} !</p>
      <h3>
        Points: <b>{{ points }} </b>
      </h3>
      <h2>
        Time Left: <b>{{ timeLeft }}</b>
      </h2>
    </div>

    <div class="answer-message" v-show="answered">
      <span
        :style="{
          backgroundColor: answerStyle,
          fontWeight: 'bold',
          color: 'white',
          display: 'block',
          fontSize: '1.5rem',
        }"
        >{{ messageScreen }}</span
      >
      <h3><b>The right Answer is:</b></h3>
      <div class="answer-message__book">
        <img class="answer-message__image" :src="correctProductImage" />
        <b> {{ correctProductTitle }} </b>
        <p>{{ correctBookDescription }}</p>
      </div>
      <button class="acept-button" @click="restartBooksAfterAnswer()">Aceptar</button>
    </div>

    <div v-show="!answered" class="play-container">
      <div class="books-container">
        <book
          v-for="book in books"
          :key="book.id"
          :book="book"
          :compareBookWithDescription="compareBookWithDescription"
        >
        </book>
      </div>

      <random-description
        :correctBookId="correctBookId"
        :correctBookDescription="correctBookDescription"
        :itemDragStart="itemDragStart"
      >
      </random-description>
    </div>
  </div>
</template>
<script>
import axios from "axios";
import { toRefs, reactive } from "@vue/reactivity";
import Book from "../components/Book.vue";
import { watch } from "@vue/runtime-core";
import RandomDescription from "../components/RandomDescription.vue";
import End from "../components/End.vue";
export default {
  components: { Book, RandomDescription, End },
  name: "Playing",
  setup() {
    const gameState = reactive({
      end: false,
      timeLeft: 60,
      timeInterval: "",
      finish: false,
      booksNumberChallenge: 2,
      messageScreen: "",
      answered: false,
      answerStyle: "",
    });

    const booksState = reactive({
      books: [],
      keyWord: "art",
      queryKeywords: ["art", "terror", "car", "horror", "love", "war", "king"],
      startIndex: 0,
      maxResults: 40,
    });

    const correctBookState = reactive({
      correctBookId: "",
      correctProductTitle: "",
      correctProductImage: "",
      correctBookDescription: "",
    });

    const playerState = reactive({
      points: 0,
      name: "",
    });

    playerState.name = localStorage.getItem("playerName");
    startTime();
    watch(() => {
      if (gameState.timeLeft == 0) {
        endGame();
      }
    });

    function endGame() {
      var element = document.getElementById("endModal");
      element.classList.add("is-active");
    }

    function closeEndModal() {
      var element = document.getElementById("endModal");
      element.classList.remove("is-active");
    }

    function compareBookWithDescription(bookId) {
      correctBookState.correctBookId == bookId ? answer(true) : answer(false);
      gameState.answered = true;
      stopTime();
    }

    function answer(type) {
      if (type == true) {
        playerState.points++;
        gameState.messageScreen = "Good Answer";
        gameState.answerStyle = "green";
      } else {
        gameState.messageScreen = "Wrong Answer";
        gameState.answerStyle = "red";
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
      correctBookState.correctBookDescription =
        booksState.books[0].volumeInfo.description;
      correctBookState.correctProductTitle =
        booksState.books[0].volumeInfo.title;
      correctBookState.correctProductImage =
        booksState.books[0].volumeInfo.imageLinks.thumbnail;
    })();

    async function getBooks() {
      try {
        const wordIndex = getRandomInt(0, booksState.queryKeywords.length);
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
        "description" in book.volumeInfo
        // && book.volumeInfo.language === "en"
      ) {
        return true;
      } else {
        return false;
      }
    }

    function restartBooksAfterAnswer() {
      (async () => {
        booksState.books = await getBooks();
        console.log("Después de esperar estos son los lirbos");
        console.log(booksState.books);
        correctBookState.correctBookId = booksState.books[0].id;
        correctBookState.correctBookDescription =
          booksState.books[0].volumeInfo.description;
        correctBookState.correctProductTitle =
          booksState.books[0].volumeInfo.title;
        correctBookState.correctProductImage =
          booksState.books[0].volumeInfo.imageLinks.thumbnail;
        gameState.answered = false;

        startTime();
      })();
    }

    function getRandomElements(array, numberOfElements) {
      const randomElements = [];
      console.log("Este es el array total");
      console.log(array);
      let index = 0;
      while (index < numberOfElements) {
        const i = Math.floor(Math.random() * numberOfElements);
        console.log("Este es el index");
        console.log(i);
        const randomItem = array[i];
        const exist = randomElements.filter(
          (element) => element.id == randomItem.id
        );
        console.log(exist);
        if (exist.length == 0) {
          randomElements.push(randomItem);
          index++;
        }
      }

      console.log("ESTE ES EL RANDOM ");
      console.log(randomElements);
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
      endGame,
      closeEndModal,
    };
  },
};
</script>
<style scoped>
.dashboard {
  background-color: var(--white);
  color: var(--primary);
  font-size: 1.5rem;
}
.playing {
  height: 100vh;
  background-color: var(--primary);
  color: var(--white);
}
.answer-message__book {
  border: solid 1px var(--black);
  border-radius: 8px;
  padding: 16px;
  margin-inline: 16px;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.books-container {
  display: flex;
  flex-direction: row;
  justify-content: center;
  gap: 100px;
  margin-block-end: 64px;
}
.play-container {
  margin-block: 64px;
  display: flex;
  flex-direction: column;
  padding-inline: 64px;
}
.answer-message {
  background-color: var(--white);
  color: var(--black);
}
.acept-button {
  margin-block: 16px;
  border: solid 1px var(--contrast);
  display: inline-block;
  padding: 0.35em 1.2em;
  background-color: var(--contrast);
  color: var(--white);
  font-size: 1.5rem;
}

.answer-message__image{
  display: block;
}

</style>