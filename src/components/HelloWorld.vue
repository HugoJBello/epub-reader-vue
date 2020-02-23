<template>
  <v-container>
    <v-row class="text-center">
      <EpubViewer :ebook="ebook"></EpubViewer>
    </v-row>
  </v-container>
</template>
<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import EpubViewer from './EpubViewer.vue'
import {Ebook} from '../models/ebook'

@Component({
  components:{EpubViewer}
})
export default class HelloWorld extends Vue {
  @Prop() private msg!: string;
  public epubUrl = "https://s3.amazonaws.com/moby-dick/moby-dick.epub";
  public ebook: Ebook = {url:this.epubUrl}
  public book :any;

  mounted(){
      
  }
  
}
</script>