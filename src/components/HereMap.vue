<template>
  <div>
    <button @click="calculateRoute">calc</button>
    <div ref="mapRef" style="width: 100vw; height: 100vh"></div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from "vue";
import H from "@here/maps-api-for-javascript/bin/mapsjs.bundle.harp.js";

const mapRef = ref();
let map = null;
let platform = null;
let apiKey = "CcuUelWr_tQlaW2HBBPs3QCw5-fz80A9pr69v9O_P9E";

function initializeMap() {
  platform = new H.service.Platform({
    apikey: apiKey,
  });

  const engineType = H.Map.EngineType["HARP"];
  const defaultLayers = platform.createDefaultLayers({
    engineType,
    pois: true,
  });

  console.log(defaultLayers);

  const newMap = new H.Map(mapRef.value, defaultLayers.vector.normal.map, {
    engineType,
    pixelRatio: window.devicePixelRatio || 1,

    zoom: 4,
    padding: { top: 50, right: 50, bottom: 50, left: 50 },
  });

  new H.mapevents.Behavior(new H.mapevents.MapEvents(newMap));
  map = newMap;
}

function onSuccess(res) {
  const route = res.routes[0];

  const lineStrings = [];

  route.sections.forEach((element) => {
    lineStrings.push(H.geo.LineString.fromFlexiblePolyline(element.polyline));
    console.log(element);
  });

  const multiLineString = new H.geo.MultiLineString(lineStrings);

  const routeLine = new H.map.Polyline(multiLineString, {
    style: {
      strokeColor: "blue",
      lineWidth: 5,
    },
  });

  const group = new H.map.Group();
  group.addObjects([routeLine]);
  // Add the group to the map
  map.addObject(group);

  // Set the map viewport to make the entire route visible:
  map.getViewModel().setLookAtData({
    bounds: group.getBoundingBox(),
  });
}

function calculateRoute() {
  const router = platform.getRoutingService(null, 8);
  const routeRequestParams = {
    routingMode: "fast",
    transportMode: "truck",
    origin: "64.144,-21.94",
    destination: "45.33,116.55",
    return: "polyline",
  };

  router.calculateRoute(routeRequestParams, onSuccess);
}

onMounted(() => {
  initializeMap();
});
</script>

<style scoped></style>
