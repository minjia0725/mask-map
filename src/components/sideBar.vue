<template>
  <div>
    <div class="sideBar">
      <div class="sideBar-header px-2 py-1">
        <div
          class="sideBar-date d-flex justify-content-between align-items-center"
        >
          <span class="h1">星期{{ todayWeek }}</span>
          <span> {{ getdate }}</span>
        </div>
        <div class="d-flex mb-2">
          <select
            class="custom-select"
            v-model="citySelect"
            @change="
              getAreas();
              focusSel = 'citySel';
            "
          >
            <option value disabled selected hidden>請選擇縣市</option>
            <option :value="city" v-for="city in cities" :key="city">
              {{ city }}
            </option>
          </select>
          <select
            class="custom-select ml-2"
            :disabled="!citySelect"
            v-model="areaSelect"
            @change="focusSel = 'areaSel'"
          >
            <option value disabled selected hidden>請選擇地區</option>
            <option :value="area" v-for="area in areas" :key="area">
              {{ area }}
            </option>
          </select>
        </div>
        <div class="form-group sideBar-search mb-1">
          <input
            id="search"
            name="search"
            type="text"
            class="form-control"
            placeholder="搜尋區域,地址,藥局"
            v-model="keyWord"
            @focus="
              citySelect = '';
              areaSelect = '';
            "
          />
          <label
            for="search"
            class="sideBar-search-icon"
            @click.prevent="listToggle = !listToggle"
          >
            <i class="fas fa-search-location"></i>
          </label>
          <ul
            class="list-group sideBar-search-list rounded-0"
            v-if="listToggle && keyWord"
          >
            <li
              class="list-group-item list-group-item-action d-flex py-2 text-black-50"
              @click="
                keyWord = item.properties.name;
                focusSel = '';
                listToggle = false;
              "
              v-for="item in filterData"
              :key="item.properties.id"
            >
              <i class="fas fa-map-marker-alt mr-2 mt-1"></i>
              <div>
                <p class="mb-0">
                  <span class="font-weight-bold text-dark">{{
                    item.properties.name
                  }}</span>
                  <br />
                  <span class="">{{ item.properties.address }}</span>
                </p>
              </div>
            </li>
          </ul>
        </div>
      </div>
      <div class="sideBar-content">
        <cards
          :allData.sync="filterData"
          :newCenter="newCenter"
          @newCenter="emitNewCenter"
        ></cards>
      </div>
    </div>
  </div>
</template>

<script>
import cards from "@/components/cards.vue";
import Cities from "@/assets/cityName.json";

export default {
  props: ["allData"],
  name: "sideBar",
  components: {
    cards,
  },
  data() {
    return {
      secrch: false,
      citySelect: "",
      cities: [],
      areaSelect: "",
      listToggle: false,
      areas: [],
      keyWord: "",
      focusSel: "",
      date: new Date(),
      todayWeek: "",
      newCenter: "",
    };
  },
  methods: {
    getCities() {
      const vm = this;
      vm.cities = Cities.map((item) => item.CityName);
    },
    getAreas() {
      const vm = this;
      let areaList = Cities.find((item) => item.CityName === vm.citySelect);
      vm.areas = areaList.AreaList.map((area) => area.AreaName);
      vm.areaSelect = "";
      vm.keyWord = "";
      vm.focusSel = false;
    },
    getDay() {
      const vm = this;
      const week = String(vm.date).substr(0, 3);
      if (week === "Mon") {
        vm.todayWeek = "一";
      } else if (week === "Tue") {
        vm.todayWeek = "二";
      } else if (week === "Wed") {
        vm.todayWeek = "三";
      } else if (week === "Thu") {
        vm.todayWeek = "四";
      } else if (week === "Fri") {
        vm.todayWeek = "五";
      } else if (week === "Sat") {
        vm.todayWeek = "六";
      } else if (week === "Sun") {
        vm.todayWeek = "日";
      }
    },
    // 處理選取或點選卡片的位置並移動地圖
    emitNewCenter(newCenter) {
      const vm = this;
      vm.$emit("getNewCenter", newCenter);
    },
  },
  computed: {
    filterData() {
      //過濾選取的地區
      const vm = this;
      if (vm.keyWord) {
        return vm.allData.filter((item) => {
          return `${item.properties.name}${item.properties.address}${item.properties.town}`.includes(
            vm.keyWord
          );
        });
      } else if (vm.citySelect) {
        return vm.allData.filter((item) => {
          if (vm.focusSel === "citySel") {
            return item.properties.county === vm.citySelect;
          } else if (vm.focusSel === "areaSel") {
            return (
              item.properties.town === vm.areaSelect &&
              item.properties.county === vm.citySelect
            );
          }
        });
      }
    },
    getdate() {
      //取得日期
      return `${new Date().getFullYear()}-${
        new Date().getMonth() + 1
      }-${new Date().getDate()}`;
    },
  },
  watch: {
    //監控filterData值變化觸發事件
    filterData() {
      const vm = this;
      if (vm.filterData) {
        const len = vm.filterData.length;
        if (len > 0) {
          const coordinates = [...vm.filterData[0].geometry.coordinates];
          vm.newCenter = [coordinates[1], coordinates[0]];
          vm.emitNewCenter(vm.newCenter);
        }
      }
    },
  },
  created() {
    this.getCities();
    this.getDay();
  },
};
</script>