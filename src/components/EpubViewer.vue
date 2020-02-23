<template>
  <v-row class="container">
    <v-col name="prev-btn" :goToPrevPage="goToPrevPage" cols="1">
      <div id="prev" @click="goToPrevPage">
        <v-icon>mdi-arrow-left-thick</v-icon>
      </div>
    </v-col>
    <v-col name="book-content" :ready="ready" cols="8">
      <div id="viewer"></div>
    </v-col>
    <v-col name="next-btn" :goToNextPage="goToNextPage" cols="1">
      <div id="next" @click="goToNextPage">
        <v-icon>mdi-arrow-right-thick</v-icon>
      </div>
    </v-col>
  </v-row>
</template>
<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import { Ebook } from "../models/ebook";
import Epub from "epubjs";
@Component
export default class EpubViewer extends Vue {
  @Prop() private ebook!: Ebook;
  public book: any;
  public rendition: any;
  public width: number = window.innerWidth;
  public height: number = window.innerHeight;
  public contentBookModify: number = 0;
  public locations:any;

  async mounted() {
    this.book = Epub(this.ebook.url) as any;
    await this.book.loaded.navigation;
    await this.initReader();
    //this.resizeToScreenSize()
  }
  async initReader() {
    this.rendition = this.book.renderTo("viewer", {
      flow: "paginated",
      contained: true
    });
    await this.rendition.display();

    await this.book.ready;
    await this.book.locations.generate()
    this.locations = JSON.parse(await this.book.locations.save())

    console.log(this.locations)
    console.log(this.rendition);
    this.rendition.next();
    console.log(this.rendition);
  }

  updateScreenSizeInfo() {
    this.width = Math.max(
      document.documentElement.clientWidth,
      window.innerWidth || 0
    );
    this.height = Math.max(
      document.documentElement.clientHeight,
      window.innerHeight || 0
    );
  }

  resizeToScreenSize() {
    this.updateScreenSizeInfo();
    this.rendition.resize(this.width, this.height);
  }

  goToPrevPage() {
    this.rendition.prev();
  }
  goToNextPage() {
    this.rendition.next();
  }
}
</script>