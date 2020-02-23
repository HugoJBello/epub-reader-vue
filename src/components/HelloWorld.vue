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
  public ebook: Ebook = {url:this.epubUrl, cfi:"epubcfi(/6/16[xchapter_002]!/4/2/2/2/2[c002p0000]/1:0)"} as Ebook;
  public book: any;

  mounted(){
      
  }
  
}
</script>