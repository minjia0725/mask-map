<template>
  <div>
    <v-map
      ref="myMap"
      :zoom="zoom"
      :center="defultPosition"
      style="height: 100vh"
    >
      <v-tilelayer :url="url"></v-tilelayer>
      <v-marker-cluster>
        <v-marker
          v-for="(item, i) in allData"
          :lat-lng="[
            item.geometry.coordinates[1],
            item.geometry.coordinates[0],
          ]"
          :key="i"
        >
          <v-popup
            :options="{
              minWidth: 280,
            }"
          >
            <h5 class="mb-0">
              <a
                target="_blank"
                class="text-primary d-block"
                :href="`https://www.google.com.tw/maps/place/${[
                  item.geometry.coordinates[1],
                  item.geometry.coordinates[0],
                ]}`"
                >{{ item.properties.name }}</a
              >
            </h5>
            <div class="text-black-50">
              <p class="mb-2 mt-1">
                <span class="mr-2"><i class="fas fa-map-marker-alt"></i></span
                >{{ item.properties.address }}
              </p>
              <p class="mb-2 mt-1">
                <span class="mr-1"><i class="fas fa-phone-alt"></i></span
                >{{ item.properties.phone }}
              </p>
            </div>
            <div class="d-flex mt-1 h6">
              <div
                class="px-3 py-2 mb-0 bg-primary d-flex justify-content-between w-50 rounded-pill text-white"
              >
                <span>成人口罩</span>
                <span>{{ item.properties.mask_adult }}</span>
              </div>
              <div
                class="px-3 py-2 mb-0 bg-secondary d-flex justify-content-between w-50 rounded-pill ml-3 text-white"
              >
                <span>兒童口罩</span>
                <span>{{ item.properties.mask_child }}</span>
              </div>
            </div>
          </v-popup>
        </v-marker>
      </v-marker-cluster>
    </v-map>
  </div>
</template>

<script>
import { LMap, LTileLayer, LMarker, LPopup } from "vue2-leaflet";
import "leaflet/dist/leaflet.css";
import LMarkerCluster from "vue2-leaflet-markercluster";
import "leaflet.markercluster/dist/MarkerCluster.css";
import "leaflet.markercluster/dist/MarkerCluster.Default.css";
import { Icon } from "leaflet";

delete Icon.Default.prototype._getIconUrl;
Icon.Default.mergeOptions({
  iconRetinaUrl: require("leaflet/dist/images/marker-icon-2x.png"),
  iconUrl: require("leaflet/dist/images/marker-icon.png"),
  shadowUrl: require("leaflet/dist/images/marker-shadow.png"),
});

export default {
  name: "OsmMap",
  props: ["allData", "defultPosition"],
  components: {
    "v-map": LMap,
    "v-tilelayer": LTileLayer,
    "v-marker": LMarker,
    "v-popup": LPopup,
    "v-marker-cluster": LMarkerCluster,
  },
  data() {
    return {
      zoom: 16,
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      map:'',
    };
  },
  mounted() {
    this.$nextTick(() => {
      this.map = this.$refs.myMap.mapObject;
    });
  },
};
</script>

