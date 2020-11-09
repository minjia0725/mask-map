<template>
  <div id="app">
    <loading
      :active.sync="isLoading"
      :opacity="0.9"
      :background-color="'#000'"
      :color="'#ff5722'"
    ></loading>
    <div class="row no-gutters" style="height: 100%">
      <side-bar
        :allData.sync="data"
        @getNewCenter="updatePosition"
        class="col-sm-6 col-md-4 col-xl-3"
      ></side-bar>
      <mask-map
        :allData.sync="data"
        :defultPosition="defultPosition"
        class="col-sm-6 col-md-8 col-xl-9 d-none d-sm-block"
        ref="myMap"
      ></mask-map>
    </div>
  </div>
</template>

<script>
import maskMap from "@/components/maskMap.vue";
import sideBar from "@/components/sideBar.vue";

export default {
  name: "App",
  data() {
    return {
      data: [],
      isLoading: false,
      adultDaily: 400,
      childDaily: 200,
      defultPosition: [25.033493, 121.564101],
    };
  },
  components: {
    maskMap,
    sideBar,
  },
  methods: {
    getData() {
      const vm = this;
      const api = process.env.VUE_APP_API;
      this.$http.get(api).then((res) => {
        vm.data = res.data.features;
      });
    },
    updatePosition(newPosition) {
      //取得子組件給的位置去更新地圖位置
      const vm = this;
      const { map } = vm.$refs.myMap;
      map.setView(newPosition,20);
    }
  },
  created() {
    this.getData();
  },
  mounted() {
    this.$nextTick(() => {
      // 獲得目前位置
      navigator.geolocation.getCurrentPosition((position) => {
        const p = position.coords;
        // 將中心點設為目前的位置
        this.defultPosition = [p.latitude, p.longitude];
      });
    });
  },
};
</script>

<style lang="scss">
@import "@/assets/scss/all.scss";
</style>
