<template>
  <div id="app">
    <v-app>
      <Doc ref="modal" :item="newdoc" :items="items" />
      <v-layout row>
        <v-flex xs12 sm6 offset-sm3>
          <v-card>
            <Docs :items="items"/>
            <v-card-text tyle="height: 100px; position: relative">
              <v-btn @click="openModal"
                big
                color="pink"
                dark
                absolute
                bottom
                right
                fab
              >
                <v-icon>add</v-icon>
              </v-btn>
            </v-card-text>
          </v-card>
        </v-flex>
      </v-layout>
    </v-app>
  </div>
</template>

<script>
import Docs from './components/Docs';
import Doc from './components/Doc';
import sheetsu from 'sheetsu-node';

let db = sheetsu({ 
  address: "https://sheetsu.com/apis/v1.0su/05755177b2fb"});

export default {
  name: 'app',
  components: {
    Docs, Doc
  },
  created () {
      this.getdocs();
  },
  methods: {
    openModal() {
      this.$refs.modal.show();
    },
    getdocs() {
      db.read().then((data) => {
        this.items = JSON.parse(data);
      });
    }
  },
  data: () => {
    return {
      newdoc: {
        id: -1,
        name: "New Document",
        exp: "",
        alert1year: true,
        alert6months: true,
        alert3months: true,
        alert1month: true
      },
      items: []
    }
  }
}
</script>
<style>
</style>