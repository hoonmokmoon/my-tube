<template>

  <component :is="$store.state.mobile ? 'div': 'v-container'">
    <h2 class="mt-2 ml-1 mb-2">인기 동영상</h2>

    <v-layout row wrap>
      <v-flex xs6 sm4 lg3 xl2 v-for="(row, i) in itemList" :key="row.id">
        <nuxt-link :to="'/item/'+row.id">
          <VideoCard
            :item="row"
            :count="i"
          ></VideoCard>
        </nuxt-link>
      </v-flex>

    </v-layout>

    <AdSense/>

    <div class="text-xs-center" @click="pageClick">
      <v-pagination :length="pageCount" :total-visible="6" v-model="param.page" :color="$store.state.bcolor"/>
    </div>

  </component>

</template>

<script>

  import VideoCard from '~/components/video-card';
  import AdSense from '~/components/ad-sense';


  export default {

    components: {
      VideoCard,
      AdSense,
    },

    async asyncData({app, params, store, query}) {

      let pageparam = {
	      page: params.page ? parseInt(params.page) : 1,
        limit: 36
      };

      const result = await app.$axios.$get("/api/video/hot-video", {params:pageparam});
      return {
        param : pageparam,
        itemList: result.videolist,
        pageCount : Math.ceil(result.count / pageparam.limit),
      }

    },

    methods: {
      async getItemList() {
        const res = await this.$axios.$get("/api/video/list", {params:this.param})
        this.itemList = res.videolist;
        window.scrollTo(0, 0);
      },

      pageClick() {
	      this.$router.push('/hotcf/'+this.param.page);
        this.getItemList();
      }

    }

  }

</script>
