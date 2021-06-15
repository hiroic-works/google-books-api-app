<template>
  <div>
    <v-row>
      <v-col cols="12">
        <v-card class="mx-auto">
          <v-row>
            <v-col cols="4">
              <v-img :src="book.image"></v-img>
            </v-col>
            <v-col cols="8">
              <v-card-title>
                {{ book.title }}
              </v-card-title>
              読んだ日
              <v-menu
                v-model="menu"
                :close-on-content-click="false"
                :nudge-right="40"
                transition="scale-transition"
                offset-y
                min-width="auto"
              >
                <template v-slot:activator="{ on, attrs }">
                  <v-text-field
                    v-model="date"
                    readonly
                    v-bind="attrs"
                    v-on="on"
                  ></v-text-field>
                </template>
                <v-date-picker
                  v-model="date"
                  @input="menu = false"
                  locale="JP-ja"
                  :day-format="(date) => new Date(date).getDate()"
                ></v-date-picker>
              </v-menu>
              <v-spacer></v-spacer>
              感想<v-textarea class="mx-2" v-model="book.memo"></v-textarea>
              <v-spacer></v-spacer>
              <v-card-actions>
                <v-btn color="secondary" to="/">
                  一覧に戻る
                </v-btn>
                <v-btn color="primary" @click="updateBookInfo()">
                  保存する
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
  name: "BookEdit",
  props: {
    books: Array,
  },
  data() {
    return {
      book: "",
      date: "",
      menu: false,
    };
  },
  methods: {
    // $emitで親コンポーネントにデータを送る
    updateBookInfo() {
      this.$emit('update-book-info', {
        id: this.$route.params.id,
        readDate: this.date,
        memo: this.book.memo
      })
    },
  },
  // ナビゲーションガードで処理
  // vueコンポーネントはそれぞれ非同期で更新されるのでApp.vueからpropsでこのコンポーネントに上手くデータが渡せない場合がある。なので全てのDOMが更新されてから処理を行う必要があるため、下記ナビゲーションガードを使用。
  beforeRouteEnter(to, from, next) {
    // thisでアクセスできないので引数を取ってアクセスする
    next((vm) => {
      // 更にDOM更新処理を待つ
      vm.$nextTick(() => {
        // 更に待たないと更新できないことがある…。
        setTimeout(() => {
          vm.book = vm.books[vm.$route.params.id];
          // 読んだ日がストレージにあればストレージを参照してなければ本日の日付を出す
          if(vm.book.readDate) {
            vm.date = vm.book.readDate
          } else {
            vm.date = new Date().toISOString().substr(0, 10)
          }
        }, 10);
      });
    });
  },
};
</script>

<style></style>
