<template>
      <div class="w-screen">
            
            <Quotes :message="quoteMessage" :speaker="quoteSpeaker" />

            <div class="flex-wrap justify-between w-full mx-auto sm:w-5/6 sm:flex">
                  <div class="sm:w-[33%] p-2 my-4" v-for="book in books" :key="book.id">
                        <router-link :to="{name : 'ShowBook' , params : {slug : book.slug}}">
                              <img class="w-full transition hover:scale-105" :src="book.image" alt="">
                        </router-link>
                        <div class="flex justify-between p-1">
                              <div>
                                    <h1 class="text-lg">{{ book.name }}</h1>
                              </div>
                              <a class="px-2 py-1 text-xl text-white bg-teal-500 rounded-full h-fit"  :href="`http://localhost:8000/api/client/download/${book.file}`"  download>
                                    <i class="fa-solid fa-download"></i>
                              </a>    
                        </div>
                  </div>
            </div>

            <div v-show="page < lastPage" class="flex justify-center p-6">
                  <button class="animate-bounce" @click="addBooks">see more</button>
            </div>
      </div>
</template>

<script>
import axios from 'axios'
import Quotes from '../../components/Quotes.vue'
      export default {
            components : {
                  Quotes
            },

            data () {
                  return {
                        quoteMessage : `"A book is a magical garden you can carry in your pocket"`,
                        quoteSpeaker : "Neil Gaiman",
                        books : [],
                        page : 1,
                        lastPage : null,
                  }
            },

            mounted () {
                  this.getAllBooks();
            },

            methods : {
                  getAllBooks() {
                        axios.get(`http://localhost:8000/api/client/get-all-books?page=${this.page}`)
                              .then((response) => {
                                    this.books.push(...response.data.data)
                                    this.lastPage = response.data.meta.last_page
                              })
                  },
                  addBooks () {
                        this.page++;
                        this.getAllBooks();
                  }
            }

      }
</script>

<style lang="scss" scoped>

</style>