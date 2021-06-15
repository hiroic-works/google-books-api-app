<template>
  <v-app>
    <Header @delete-local-storage="deleteLocalStorage" />
    <v-main>
      <v-container>
        <router-view
          @add-book-list="addBook"
          @update-book-info="updateBookInfo"
          :books="books"
        />
      </v-container>
    </v-main>
    <Footer />
  </v-app>
</template>

<script>
import Header from "@/global/Header";
import Footer from "@/global/Footer";

const STRORAGE_KEY = "book";

export default {
  name: "App",

  components: {
    Header,
    Footer,
  },

  data() {
    return {
      books: [],
      newBook: null,
    };
  },
  mounted() {
    // ストレージからデータ取得
    if (localStorage.getItem(STRORAGE_KEY)) {
      try {
        this.books = JSON.parse(localStorage.getItem(STRORAGE_KEY));
      } catch (e) {
        localStorage.removeItem(STRORAGE_KEY);
      }
    }
  },
  methods: {
    // 引数設定して子コンポーネント(BookSearch.vue)からのデータを受け取る
    addBook(e) {
      // 配列に子からのデータを保存
      this.books.push({
        id: this.books.length,
        title: e.title,
        image: e.image,
        description: e.description,
        readDate: "",
        memo: "",
      });

      // this.newBook = "";

      // ストレージに保存する
      this.saveBooks();

      //最後に追加したIDを取得。array.slice(-1)[0]で最後の配列が取得できる
      // console.log(this.books.slice(-1)[0].id)
      // 追加した本のeditページに遷移
      this.goToEditPage(this.books.slice(-1)[0].id);
    },
    // 引数設定して子コンポーネント(BookEdit.vue)からのデータを受け取る
    updateBookInfo(e) {
      // 更新データを格納する配列を作成
      const updateInfo = {
        id: e.id,
        readDate: e.readDate,
        memo: e.memo,
        title: this.books[e.id].title,
        image: this.books[e.id].image,
        description: this.books[e.id].description,
      };
      // 更新するデータの配列場所(e.id)を指定して更新
      this.books.splice(e.id, 1, updateInfo);

      // ストレージに保存する
      this.saveBooks();

      // トップページにリダイレクト
      this.$router.push(`/`);
    },
    // 本の削除
    removeBook(x) {
      this.books.splice(x, 1);
      this.saveBooks();
    },
    // 本の保存
    saveBooks() {
      const parsed = JSON.stringify(this.books);
      localStorage.setItem(STRORAGE_KEY, parsed);
    },
    // BookEdit.vueに遷移
    goToEditPage(id) {
      this.$router.push(`/edit/${id}`);
    },
    // 本の全削除
    deleteLocalStorage() {
      const isDeleted = "LocalStorageのでーたを差所してもよろしいですか";
      if (window.confirm(isDeleted)) {
        // 各データの削除
        localStorage.setItem(STRORAGE_KEY, "");
        localStorage.removeItem(STRORAGE_KEY);
        this.books = [];
        // 削除後にDOMを更新するためにリロードする。computedで定義してたら大丈夫だが、data()で定義したものは残る事がある
        window.location.reload();
      }
    },
  },
};
</script>
