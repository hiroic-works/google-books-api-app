<template>
  <div>
    <v-row>
      <v-col cols="6">
        <v-text-field label="本のタイトルを検索" v-model="keyword">
        </v-text-field>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="3">
        <v-btn color="primary" @click="search(keyword)">検索する</v-btn>
      </v-col>
      <v-col cols="3">
        <v-btn color="secondary" to="/">
          一覧に戻る
        </v-btn>
      </v-col>
    </v-row>
    <v-row>
      <v-col
        class="my-2"
        cols="12"
        md="6"
        v-for="(book, index) in searchResults"
        :key="book.index"
      >
        <v-card class="mx-auto">
          <v-row>
            <v-col cols="4">
              <v-img :src="book.image"></v-img>
            </v-col>
            <v-col cols="8">
              <v-card-title>
                {{ book.title }}
              </v-card-title>
              {{ book.description }}
              <v-spacer></v-spacer>
              <v-card-actions>
                <v-btn fab dark color="indigo" @click="addBookList(index)">
                  <v-icon dark>mdi-plus</v-icon>
                </v-btn>
              </v-card-actions>
            </v-col>
          </v-row>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
export default {
  name: "BookSearch",
  data() {
    return {
      keyword: "",
      searchResults: [],
    };
  },
  methods: {
    // $emitで親コンポーネント(App.vue)に本のデータを送る
    addBookList(index) {
      this.$emit('add-book-list',this.searchResults[index])
    },
    // google book apiから本を取得する
    async search(keyword) {
      this.searchResults = [];
      const baseURL = "https://www.googleapis.com/books/v1/volumes?";
      const params = {
        q: `intitle:${keyword}`,
        maxResults: 20,
      };
      const qParams = new URLSearchParams(params);
      // console.log(baseURL + qParams);
      // 本取得
      const response = await fetch(baseURL + qParams).then((response) =>
        response.json()
      );
      // console.log(response.items);

      // 取得したデータを配列に格納する
      for (let book of response.items) {
        let title = book.volumeInfo.title;
        let image = book.volumeInfo.imageLinks;
        let description = book.volumeInfo.description;

        this.searchResults.push({
          title: title ? title : "",
          image: image ? image.thumbnail : "",
          description: description ? description.slice(0, 40) : "",
        });
      }
    },
  },
};
</script>

<style></style>
