<template>
  <div>
    <div
      class="px-2 py-2 border-bottom cards"
      v-for="item in allData"
      :key="item.properties.id"
      @click="emitNewCenter(item)"
    >
      <h2 class="mb-1 h4">
        {{ item.properties.name }}
      </h2>
      <div class="text-black-50">
        <p class="mb-1">
          <span class="mr-2"><i class="fas fa-map-marker-alt"></i></span
          >{{ item.properties.address }}
        </p>
        <span
          ><span class="mr-1"><i class="fas fa-phone-alt"></i></span
          >{{ item.properties.phone }}</span
        >
        <br />
      </div>
      <div class="d-flex mt-1">
        <div
          class="px-3 py-2 mb-0 bg-primary d-flex justify-content-between w-50 rounded-pill text-white"
        >
          <span>成人口罩</span><span>{{ item.properties.mask_adult }}</span>
        </div>
        <div
          class="px-3 py-2 mb-0 bg-secondary d-flex justify-content-between w-50 rounded-pill ml-3 text-white"
        >
          <span>兒童口罩</span><span>{{ item.properties.mask_child }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Cards",
  props: ["allData"],
  data() {
    return {};
  },
  methods: {
    //傳送點擊藥局的位置到父層
    emitNewCenter(item) {
      const vm = this;
      const newPosition = item.geometry.coordinates;
      const newCenter = [newPosition[1], newPosition[0]];
      vm.$emit("newCenter", newCenter);
    },
  },
};
</script>