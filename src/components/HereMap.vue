<script setup lang="ts">
// @ts-expect-error: TypeScript does not recognize this module
import H from "@here/maps-api-for-javascript/bin/mapsjs.bundle.harp.js";

import { onMounted, ref } from "vue";
import * as turf from "@turf/turf";

type CalcRouteQueryParams = {
  transportMode: "truck";
  origin: string;
  destination: string;
  via?: string[];
  routingMode?: "fast" | "short";
  returnInfo?: "polyline" | "summary" | "travelSummary";
  lang?: "en-US";
};

const fuelStations = [
  {
    id: 71,
    name: "Pilot Travel Center",
    store_number: "146",
    city: "Castleton-On-Hudson",
    state: "NY",
    address: "995 US Route 9",
    country: "United States",
    longitude: -73.674474,
    latitude: 42.49792,
    time_zone: "UTC -4:0",
    interstate: "I-90, Exit 12",
    actual_price: 3.25,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:08",
    updated_at: "2025-02-22T22:00:08",
  },
  {
    id: 74,
    name: "Pilot Travel Center",
    store_number: "150",
    city: "Hagerstown",
    state: "MD",
    address: "11633 Greencastle Pike",
    country: "United States",
    longitude: -77.807115,
    latitude: 39.633648,
    time_zone: "UTC -4:0",
    interstate: "I-70 &  US 63",
    actual_price: 3.31,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:08",
    updated_at: "2025-02-22T22:00:08",
  },
  {
    id: 88,
    name: "Pilot Travel Center",
    store_number: "179",
    city: "Hagerstown",
    state: "MD",
    address: "16921 Halfway Blvd",
    country: "United States",
    longitude: -77.782978,
    latitude: 39.629637,
    time_zone: "UTC -4:0",
    interstate: "I-81, Exit 5B",
    actual_price: 3.3,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:08",
    updated_at: "2025-02-22T22:00:08",
  },
  {
    id: 90,
    name: "Pilot Travel Center",
    store_number: "190",
    city: "Hampton",
    state: "NJ",
    address: "66 NJ-173 West",
    country: "United States",
    longitude: -74.968481,
    latitude: 40.636946,
    time_zone: "UTC -4:0",
    interstate: "I-78, Exit 12 Westbound",
    actual_price: 3.37,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:08",
    updated_at: "2025-02-22T22:00:08",
  },
  {
    id: 100,
    name: "Pilot Travel Center",
    store_number: "210",
    city: "Mahwah",
    state: "NJ",
    address: "230 Route 17 South",
    country: "United States",
    longitude: -74.155581,
    latitude: 41.091971,
    time_zone: "UTC -4:0",
    interstate: "Route 17 South",
    actual_price: 3.39,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:08",
    updated_at: "2025-02-22T22:00:08",
  },
  {
    id: 118,
    name: "Pilot Travel Center",
    store_number: "253",
    city: "Carneys Point",
    state: "NJ",
    address: "600 Pennsville-Auburn Road",
    country: "United States",
    longitude: -75.479327,
    latitude: 39.681294,
    time_zone: "UTC -4:0",
    interstate: "I-295, Exit 2B NJT",
    actual_price: 3.48,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:08",
    updated_at: "2025-02-22T22:00:08",
  },
  {
    id: 133,
    name: "Pilot Travel Center",
    store_number: "280",
    city: "Bloomsbury",
    state: "NJ",
    address: "979 Route 173",
    country: "United States",
    longitude: -75.073024,
    latitude: 40.659479,
    time_zone: "UTC -4:0",
    interstate: "I-78 & NJ 173, Exit 7",
    actual_price: 3.37,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:08",
    updated_at: "2025-02-22T22:00:08",
  },
  {
    id: 161,
    name: "Pilot Travel Center",
    store_number: "322",
    city: "Bath",
    state: "NY",
    address: "7767 State Route 53",
    country: "United States",
    longitude: -77.363846,
    latitude: 42.377103,
    time_zone: "UTC -4:0",
    interstate: "I-86, Exit 37",
    actual_price: 3.36,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:08",
    updated_at: "2025-02-22T22:00:08",
  },
  {
    id: 194,
    name: "Pilot Travel Center",
    store_number: "380",
    city: "Liverpool",
    state: "NY",
    address: "107 Seventh North Street",
    country: "United States",
    longitude: -76.165213,
    latitude: 43.086934,
    time_zone: "UTC -4:0",
    interstate: "I-81/90, Exit 25",
    actual_price: 3.33,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:08",
    updated_at: "2025-02-22T22:00:08",
  },
  {
    id: 203,
    name: "Pilot Travel Center",
    store_number: "394",
    city: "Newburgh",
    state: "NY",
    address: "239 Route 17K",
    country: "United States",
    longitude: -74.113466,
    latitude: 41.520759,
    time_zone: "UTC -4:0",
    interstate: "I-84, Exit 34",
    actual_price: 3.39,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:08",
    updated_at: "2025-02-22T22:00:08",
  },
  {
    id: 210,
    name: "Pilot Travel Center",
    store_number: "408",
    city: "Grantsville",
    state: "MD",
    address: "3000 Chestnut Ridge Road",
    country: "United States",
    longitude: -79.100668,
    latitude: 39.691927,
    time_zone: "UTC -4:0",
    interstate: "I-68, Exit 22",
    actual_price: 3.61,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:08",
    updated_at: "2025-02-22T22:00:08",
  },
  {
    id: 274,
    name: "Pilot Travel Center",
    store_number: "494",
    city: "Rotterdam",
    state: "NY",
    address: "1128 Duanesburg Road",
    country: "United States",
    longitude: -74.028895,
    latitude: 42.781451,
    time_zone: "UTC -4:0",
    interstate: "I-88. Exit 25",
    actual_price: 3.24,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:08",
    updated_at: "2025-02-22T22:00:08",
  },
  {
    id: 394,
    name: "Flying J",
    store_number: "688",
    city: "Carneys Point",
    state: "NJ",
    address: "326 Slapes Corner",
    country: "United States",
    longitude: -75.48568,
    latitude: 39.684643,
    time_zone: "UTC -4:0",
    interstate: "I-295 Exit 2C",
    actual_price: 3.47,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:09",
    updated_at: "2025-02-22T22:00:08",
  },
  {
    id: 399,
    name: "Flying J",
    store_number: "693",
    city: "Pembroke",
    state: "NY",
    address: "8484 Allegheny Road",
    country: "United States",
    longitude: -78.406998,
    latitude: 43.000809,
    time_zone: "UTC -4:0",
    interstate: "I-90 Exit 48A",
    actual_price: 3.4,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:09",
    updated_at: "2025-02-22T22:00:09",
  },
  {
    id: 467,
    name: "Flying J",
    store_number: "784",
    city: "North East",
    state: "MD",
    address: "1 Center Drive",
    country: "United States",
    longitude: -75.950225,
    latitude: 39.625472,
    time_zone: "UTC -4:0",
    interstate: "I-95, Exit 100",
    actual_price: 3.26,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:09",
    updated_at: "2025-02-22T22:00:09",
  },
  {
    id: 470,
    name: "Flying J",
    store_number: "875",
    city: "Elkton",
    state: "MD",
    address: "221 Belle Hill Road",
    country: "United States",
    longitude: -75.805819,
    latitude: 39.637918,
    time_zone: "UTC -4:0",
    interstate: "I-95, Exit 109A",
    actual_price: 3.42,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:09",
    updated_at: "2025-02-22T22:00:09",
  },
  {
    id: 473,
    name: "Pilot Dealer",
    store_number: "880",
    city: "Port Jervis",
    state: "NJ",
    address: "15 Route 23",
    country: "United States",
    longitude: -74.687133,
    latitude: 41.349388,
    time_zone: "UTC -4:0",
    interstate: "I-84, Exit 1",
    actual_price: 3.39,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:09",
    updated_at: "2025-02-22T22:00:09",
  },
  {
    id: 481,
    name: "Pilot Dealer",
    store_number: "891",
    city: "Ledgewood",
    state: "NJ",
    address: "1470 Route 46 East",
    country: "United States",
    longitude: -74.686196,
    latitude: 40.892362,
    time_zone: "UTC -4:0",
    interstate: "I-80 Exit 27B/Exit 27 A&B",
    actual_price: 3.39,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:09",
    updated_at: "2025-02-22T22:00:09",
  },
  {
    id: 562,
    name: "Pilot Express",
    store_number: "1098",
    city: "Newark",
    state: "NJ",
    address: "400 Doremus Avenue",
    country: "United States",
    longitude: -74.12474,
    latitude: 40.719338,
    time_zone: "UTC -4:0",
    interstate: "I-95 Exit 15W",
    actual_price: 4.16,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:09",
    updated_at: "2025-02-22T22:00:09",
  },
  {
    id: 627,
    name: "Flying J",
    store_number: "1315",
    city: "Cinnaminson",
    state: "NJ",
    address: "2601 US 130 South",
    country: "United States",
    longitude: -74.974713,
    latitude: 40.006979,
    time_zone: "UTC -4:0",
    interstate: "HWY 130",
    actual_price: 3.36,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:09",
    updated_at: "2025-02-22T22:00:09",
  },
  {
    id: 628,
    name: "Pilot Travel Center",
    store_number: "1317",
    city: "Fultonville",
    state: "NY",
    address: "164 Riverside Drive",
    country: "United States",
    longitude: -74.356256,
    latitude: 42.946194,
    time_zone: "UTC -4:0",
    interstate: "I-90 Exit 28",
    actual_price: 3.26,
    active: true,
    effective_date: "2025-02-23",
    created_at: "2024-10-03T18:00:09",
    updated_at: "2025-02-22T22:00:09",
  },
  {
    id: 706,
    name: "ONE9",
    store_number: "290",
    city: "Perryville",
    state: "MD",
    address: "31 Heather Lane",
    country: "United States",
    longitude: -76.06515,
    latitude: 39.589233,
    time_zone: "UTC -4:0",
    interstate: "I-95 & MD 222, Exit 93",
    actual_price: 0,
    active: true,
    effective_date: null,
    created_at: "2024-10-22T08:20:09",
    updated_at: "2025-02-19T11:30:39",
  },
  {
    id: 827,
    name: "Exxon",
    store_number: "1339",
    city: "Parish",
    state: "NY",
    address: "2023 NY-104",
    country: "United States",
    longitude: -76.115495,
    latitude: 43.458336,
    time_zone: "UTC -4:0",
    interstate: "I-81, Exit 34",
    actual_price: 0,
    active: true,
    effective_date: null,
    created_at: "2024-10-22T08:20:10",
    updated_at: "2025-02-19T11:30:39",
  },
];

let platform: null | H.service.Platform = null;
const apiKey = "CcuUelWr_tQlaW2HBBPs3QCw5-fz80A9pr69v9O_P9E";
const mapRef = ref();
let map: null | H.Map = null;
let ui: H.ui.UI = null;

const origin = { lat: 56.97, lng: 24.09 };
const destination = { lat: 54.7, lng: 25.24 };

function initMap() {
  platform = new H.service.Platform({
    pois: true,
    apikey: apiKey,
  });

  const engineType = H.Map.EngineType["HARP"];
  const defaultLayers = platform.createDefaultLayers({ engineType });

  defaultLayers.vector.normal.map.setMin(3);
  defaultLayers.vector.normal.mapnight.setMin(3);
  // defaultLayers.raster.satellite.map.setMin(3);

  const newMap = new H.Map(mapRef.value, defaultLayers.vector.normal.map, {
    engineType,
    center: { lat: 37.7397, lng: -121.4252 },
    zoom: 10,
    pixelRatio: window.devicePixelRatio || 1,
  });

  new H.mapevents.Behavior(new H.mapevents.MapEvents(newMap));
  ui = H.ui.UI.createDefault(newMap, defaultLayers);
  map = newMap;
}

async function calculateRoute(queryParams: CalcRouteQueryParams) {
  const router = platform.getRoutingService(null, 8);

  const {
    routingMode = "fast",
    origin,
    destination,
    returnInfo = "polyline",
    transportMode,
    via,
    lang = "en-US",
  } = queryParams;

  if (via) {
    queryParams.via = via;
  }

  const route = await router.calculateRoute({
    transportMode,
    origin,
    destination,
    routingMode,
    return: returnInfo,
    lang,
  });

  return route.routes;
}

async function drawRouteLine(routes: any[]) {
  if (!routes.length) {
    return;
  }

  const lineStrings: H.geo.LineString[] = [];
  routes[0].sections.forEach((section) => {
    // Create a linestring to use as a point source for the route line
    lineStrings.push(H.geo.LineString.fromFlexiblePolyline(section.polyline));
  });

  const routeCoordinates = lineStrings[0].toGeoJSON().coordinates;

  const turfLineString = turf.lineString(routeCoordinates);

  const fuelStationsWithinRoute = fuelStations.filter((station) => {
    const stationPoint = turf.point([station.longitude, station.latitude]);
    const distance = turf.pointToLineDistance(stationPoint, turfLineString, {
      units: "kilometers",
    });
    return distance < 1;
  });

  console.log(fuelStationsWithinRoute, "FUEL STATIONS WITHIN ROUTE");

  const markers = [];
  fuelStationsWithinRoute.forEach((station) => {
    const content = `<div>${station.actual_price}</div>`;

    const infoBubble = new H.ui.InfoBubble(
      { lat: station.latitude, lng: station.longitude },
      {
        content,
      }
    );

    infoBubble.addEventListener("tap", () => {
      console.log("Tapped on bubble");
    });

    console.log(infoBubble.getState(), "INFO BUBBLE");

    ui.addBubble(infoBubble);

    markers.push(
      new H.map.Marker({
        lat: station.latitude,
        lng: station.longitude,
      })
    );
  });

  // const items = await discoverFuelStationsOnRoute({
  //   polyline,
  // });
  // console.log(items, "ITEMS ON ROUTE");
  // const markers = items.map((item) => {
  //   return new H.map.Marker(item.position);
  // });

  const multiLineString = new H.geo.MultiLineString(lineStrings);

  const routeLine = new H.map.Polyline(multiLineString, {
    style: {
      strokeColor: "blue",
      lineWidth: 3,
    },
  });

  const startMarker = new H.map.Marker(origin);

  // Create a marker for the end point:
  const endMarker = new H.map.Marker(destination);

  // Create a H.map.Group to hold all the map objects and enable us to obtain
  // the bounding box that contains all its objects within
  const group = new H.map.Group();
  group.addObjects([routeLine, ...markers]);
  map.addObject(group);

  // Set the map viewport to make the entire route visible:
  // map.getViewModel().setLookAtData({
  //   bounds: group.getBoundingBox(),
  // });
}

async function discoverFuelStationsOnRoute(discoverQueryParams: any) {
  const { bbox, polyline } = discoverQueryParams;

  const data = await platform.getSearchService().discover(
    {
      at: "40.7128,-74.0060",
      in: "countryCode:USA",
      limit: 100,
      q: "fuel",
      route: `${polyline}`,
    },
    console.log,
    console.error
  );
  return data.items;
}

onMounted(() => {
  initMap();
  calculateRoute({
    origin: "40.7128,-74.0060", // New York City
    destination: "38.9072,-77.0369",
    transportMode: "truck",
  }).then((routes) => {
    console.log(routes, "ROUTES");
    drawRouteLine(routes);
  });
});
</script>

<template>
  <div id="here-map" ref="mapRef"></div>
</template>

<style scoped>
#here-map {
  height: 100vh;
  width: 100vw;
}
</style>
