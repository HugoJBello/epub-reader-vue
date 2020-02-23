<template>
<div id="app-book" :ready="ready">
  <v-row class="container">
    <v-col name="prev-btn" :goToPrevPage="goToPrevPage" cols="1">
      <div id="prev" @click="goToPrevPage">
        <v-icon>mdi-arrow-left-thick</v-icon>
      </div>
    </v-col>
    <v-col name="book-content" cols="9">
      {{sectionLabel}}
    </v-col>
    <v-col name="next-btn" :goToNextPage="goToNextPage" cols="1">
      <div id="next" @click="goToNextPage">
        <v-icon>mdi-arrow-right-thick</v-icon>
      </div>
    </v-col>
  </v-row>
  <v-row name="book-content" id="viewer" :ready="ready">
  </v-row>
  <v-row class="footer">
    <v-col cols="4">
    </v-col>
    <v-col cols="1">
      {{Math.round(progressValue * 100) / 100}}
    </v-col>
    <v-col cols="4">
    </v-col>
  </v-row>
</div>
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
  public ready= false;

  public nextLabel: string=""
  public nextNav: any
  public prevNav: any
  public prevLabel: string =""
  public sectionNav: any
  public sectionLabel: string=""

  public progressValue:number =0;
  public totalPages: number = 1;
  public currentPage: number = 1;
  public currentCfi:string = this.ebook.cfi || "";

  async mounted() {
    this.book = Epub(this.ebook.url) as any;
    await this.book.loaded.navigation;
    await this.initReader();

  }
  async initReader() {
    this.updateScreenSizeInfo()
    let width, height;

    if (this.width<800){
      width = 350;
      height= 400;
    } else {
      width = Math.max(this.width-700, 600);
      height= this.height-50;
    }

    this.rendition = this.book.renderTo("viewer", {
      flow: "paginated",
      contained: true,
      width, height
    });


    await this.rendition.display(this.currentCfi);

    await this.book.ready.all;

    this.loadSections()
    this.initSwipeAction();
  

    await this.initPagination();
    this.savePages()
    this.$emit('ready');
    this.ready = true;
  
  }

  savePages() {
    const location = this.rendition.currentLocation()
    this.progressValue = this.book.locations.percentageFromCfi(location.start.cfi)
    this.currentPage = location.start
    this.currentCfi = location.start.cfi
  }

  async initPagination() {
    await this.book.locations.generate();
    const locations = await this.book.locations.generate()
    this.totalPages =  locations.length
    this.currentPage = locations.start
    console.log(this.currentPage)
    console.log(this.totalPages)
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


  goToPrevPage() {
    this.rendition.prev();
    this.savePages();
  }
  goToNextPage() {
    this.rendition.next();
    this.savePages();
  }
  goToCfi(cfi:string){
    if (cfi){
    this.rendition.display(cfi)
    }
  }

  loadSections(){
    this.rendition.on("rendered", (section:any)=>{
      const nextSection = section.next();
      const prevSection = section.prev();
      this.sectionNav = this.book.navigation.get(section.href);
      this.sectionLabel = this.sectionNav.label;

      if(nextSection) {
        this.nextNav = this.book.navigation.get(nextSection.href);
        if(this.nextNav) {
          this.nextLabel = this.nextNav.label;
        } else {
          this.nextLabel = "next";
        }
      } 

      if(prevSection) {
        this.prevNav = this.book.navigation.get(prevSection.href);

        if(this.prevNav) {
          this.prevLabel = this.prevNav.label;
        } else {
          this.prevLabel = "previous";
        }

      } 
      console.log(this.sectionLabel, this.sectionNav, this.nextLabel, this.nextNav, this.prevNav, this.prevLabel)
    });
  }

  initSwipeAction(){
    this.rendition.hooks.content.register((contents: any) => {
      const el = contents.document.documentElement;

      if (el) {

        //Enable swipe gesture to flip a page
        let start: Touch;
        let end: Touch;

        el.addEventListener('touchstart', (event: TouchEvent) => {
          start = event.changedTouches[0];
        });

        el.addEventListener('touchend', (event: TouchEvent) => {
          end = event.changedTouches[0];
          const elBook = document.getElementById('app-book'); //Parent div, which contains the #area div
          if( elBook ) {
            const bound = elBook.getBoundingClientRect();
            const hr = (end.screenX - start.screenX) / bound.width;
            const vr = Math.abs((end.screenY - start.screenY) / bound.height);
            if (hr > 0.25 && vr < 0.1) return this.rendition.prev();
            if (hr < -0.25 && vr < 0.1) return this.rendition.next();
          }
        });
      }
    });
  }
}
</script>

<style lang="scss">

</style>