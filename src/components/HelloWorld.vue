<template>
  <div class="hello">
    <div id="viewer"></div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import Epub from 'epubjs'

@Component
export default class HelloWorld extends Vue {
  @Prop() private msg!: string;
  public book :any;
  public epubUrl = "http://epubtest.org/books/Fundamental-Accessibility-Tests-Basic-Functionality-v1.0.0.epub";
  mounted(){
      this.book = Epub(this.epubUrl) as any;
      const rendition = this.book.renderTo("viewer");
      rendition.display();
  }
  
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
