<!--
 * @Author: tyc 690199845@qq.com
 * @Date: 2025-06-10 08:18:24
 * @LastEditors: tyc 690199845@qq.com
 * @LastEditTime: 2025-06-10 16:28:53
 * @FilePath: /vue-leaflet-world-map/src/components/WorldMap.vue
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
<template>
  <div id="map" style="height: 100vh"></div>
</template>

<script setup>
import L from "leaflet";
import { onMounted } from "vue";

const countryNameMap = {
  China: "中国",
  UnitedStates: "美国",
  Russia: "俄罗斯",
  India: "印度",
  Brazil: "巴西",
  Canada: "加拿大",
  Australia: "澳大利亚",
  Germany: "德国",
  France: "法国",
  Japan: "日本",
  "South Korea": "韩国",
};

onMounted(() => {
  const map = L.map("map").setView([20, 0], 2);

  L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
    attribution: "© OpenStreetMap contributors",
  }).addTo(map);

  fetch(
    "https://raw.githubusercontent.com/johan/world.geo.json/master/countries.geo.json"
  )
    .then((res) => res.json())
    .then((geojson) => {
      L.geoJSON(geojson, {
        style: {
          color: "#3388ff",
          weight: 1,
          fillOpacity: 0.3,
        },
        onEachFeature: function (feature, layer) {
          const englishName = feature.properties.name;
          const chineseName = countryNameMap[englishName] || "未知国家";
          const label = `${chineseName} (${englishName})`;

          layer.on("click", function (e) {
            L.popup()
              .setLatLng(e.latlng)
              .setContent(`<strong>${label}</strong>`)
              .openOn(map);
          });

          layer.on("mouseover", () => {
            layer.setStyle({ fillColor: "#ff7800", fillOpacity: 0.5 });
          });
          layer.on("mouseout", () => {
            layer.setStyle({ fillColor: "#3388ff", fillOpacity: 0.3 });
          });
        },
      }).addTo(map);
    });
});
</script>

<style scoped>
#map {
  height: 100%;
  width: 100%;
}
</style>
