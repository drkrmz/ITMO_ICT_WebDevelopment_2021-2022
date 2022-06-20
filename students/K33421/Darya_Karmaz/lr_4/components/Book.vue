<template>
 <section>
 <v-app>
    <v-app-bar
      app
      color="black"
      dark
    >
    <v-btn @click="gohome()"
        text
      >
        <span class="mr-2">Home</span>
    </v-btn>
    <v-spacer></v-spacer>
    <v-btn v-if="!auth" @click="goSignin()"
        text
      >
        <span class="mr-2">Sign in</span>
      </v-btn>
      <v-btn v-if="!auth" @click="goLogin()"
        text
      >
        <span class="mr-2">Login</span>
      </v-btn>
      <v-btn v-if="auth" @click="edit()"
        text
      >
        <span class="mr-2">Edit profile</span>
      </v-btn>
      <v-btn v-if="auth" @click="goLogout()"
        text
      >
        <span class="mr-2">Log out</span>
      </v-btn>
    </v-app-bar>
     <v-row class="mx-3.5">
     <v-col cols="4" class="mx-auto">
<br><br><br><br>
         <div v-for="book in books" :key="book.id">
          <v-card
         elevation="2"
         class="px-6 py-5"
         color="black"
         dark
         >
         {{ book.name }}
         <br><br>
         <v-btn @click="oneBook(book.id)"> Learn more about this book </v-btn><br><br>
         <v-btn v-if="auth" @click="takeBook(book.id)"> Take this book </v-btn>
        </v-card><br><br>
         </div>
     </v-col>
   </v-row>
   <Pagination :total="total" :item="books.length" @page-changing="loadBook"/>
  </v-app>
 </section>
</template>

<script>
import $ from 'jquery'
import Pagination from '../components/Pagination'
export default {
  name: 'Book',
  data () {
    return {
      books: '',
      userid: '',
      page: 1,
      total: 0
    }
  },
  components: { Pagination },
  computed: {
    auth () {
      var auth
      if (sessionStorage.getItem('auth_token')) {
        auth = true
      }
      return auth
    }
  },
  created () {
    if (sessionStorage.getItem('auth_token')) {
      $.ajaxSetup({
        headers: { Authorization: 'Token ' + sessionStorage.getItem('auth_token') }
      })
    }
    this.loadBook(this.page)
  },
  methods: {
    goLogin () {
      this.$router.push({ name: 'Login' })
    },
    edit () {
      this.$router.push({ name: 'Edit' })
    },
    goSignin () {
      this.$router.push({ name: 'SignIn' })
    },
    goLogout () {
      sessionStorage.removeItem('auth_token')
      window.location = '/'
    },
    gohome () {
      this.$router.push({ name: 'Home' })
    },
    computed: {
      auth () {
        var auth
        if (sessionStorage.getItem('auth_token')) {
          auth = true
        }
        return auth
      }
    },
    created () {
      if (sessionStorage.getItem('auth_token')) {
        $.ajaxSetup({
          headers: { Authorization: 'Token ' + sessionStorage.getItem('auth_token') }
        })
      }
    },
    loadBook (pageNumber) {
      if (sessionStorage.getItem('auth_token')) {
        this.findUser()
      }
      $.ajax({
        url: 'http://127.0.0.1:8000/library/books/list/?page=' + pageNumber,
        type: 'GET',
        success: (response) => {
          this.books = response.results
          this.total = response.count
        },
        error: (response) => {
          alert('Something went wrong, please, try again')
        }
      })
    },
    oneBook (id) {
      this.$router.push({ name: 'OneBook', params: { id: id } })
    },
    findUser () {
      $.ajax({
        url: 'http://127.0.0.1:8000/auth/users/me/',
        headers: { Authorization: 'Token ' + sessionStorage.getItem('auth_token') },
        type: 'GET',
        success: (response) => {
          this.userid = response.id
        },
        error: (response) => {
          alert('Something went wrong, please, try again')
        }
      })
    },
    takeBook (book) {
      $.ajax({
        url: 'http://127.0.0.1:8000/library/books/take',
        type: 'POST',
        data: {
          book: book,
          user: this.userid
        },
        success: (response) => {
          alert('You have taken a book')
        },
        error: (response) => {
          alert('Something went wrong, please, try again')
        }
      })
    }
  }
}
</script>

<style>
h1
{
font-size: 200%;
}
</style>
