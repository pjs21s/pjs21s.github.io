---
layout: post
title: "vue-leaflet 마커 아이콘 나오게 하기 줌 이벤트 적용"
tags: leaflet
comments: true
---

우선 아래 코드를 작성하여 예제 맵과 마커가 지도위에 표시되게 합니다.

```javascript
// HelloWorld.vue
<template>
  <div class="hello">
    <l-map
      id="map"
      :center="center"
      :min-zoom="7"
    >
      <l-tile-layer :url="url" />

      <l-marker
        v-for="marker in markers"
        :key="marker.name"
        :lat-lng="marker.latLng"
        >
        <l-icon
          :iconUrl="markerImg"
          :shadowUrl="markerShadowImg"
        >
        </l-icon>
        </l-marker>
    </l-map>
  </div>
</template>

<script>
import { LMap, LTileLayer, LMarker, LIcon } from 'vue2-leaflet'
import { latLng } from 'leaflet'
import markerImg from 'leaflet/dist/images/marker-icon.png';
import markerShadowImg from 'leaflet/dist/images/marker-shadow.png';

export default {
  name: 'HelloWorld',
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LIcon
  },
  data() {
    return {
      url: 'http://api.vworld.kr/req/wmts/1.0.0/본인 API/Base/{z}/{y}/{x}.png',
      center: latLng(37.560364, 126.984091),

      markerImg: markerImg,
      markerShadowImg: markerShadowImg,
      markers: [
        {
          name: 'Incheon1',
          latLng: [37.43915734156776, 126.70961806645906]
        },
        {
          name: 'Incheon2',
          latLng: [37.44488540784413, 126.69788848617786]
        },
        {
          name: 'Incheon3',
          latLng: [37.44743013526278, 126.72147475390362]
        },
        {
          name: 'Seoul1',
          latLng: [37.549205, 126.99371]
        },
        {
          name: 'Seoul2',
          latLng: [37.484939, 127.039032]
        },
        {
          name: 'Seoul3',
          latLng: [37.392255, 127.131042]
        },
        {
          name: 'Ulsan',
          latLng: [35.540328, 129.256897]
        },
        {
          name: 'Busan',
          latLng: [35.150512, 129.074249]
        }
      ]      
    }
  }
}
</script>
<style scoped>
#map {
    width: 100%;
    height: 500px;
    border: 1px solid black;
  }
</style>

```

LIcon을 사용해서 아이콘 정의도 해주었습니다. 왜냐하면 공식문서에도 나와있듯이 웹팩하고 같이 쓰면 아이콘 이미지가 손실되는 문제가 있기때문에 별도로 임포트해주어 사용해야 합니다.

코드를 알맞게 고친 후 혹시 동작이 안되면 끄고 `npm run build`를 한번 하고 시작해주세요.

이렇게 마커만 올리면 재미가 없으니 마커를 클릭했을 때 확대가 되도록 이벤트를 주겠습니다.


```javascript
<l-map
      ref="map"
    >
...
<l-marker
    @click="zoomInMarker"
    >
<script>
methods: {
    zoomInMarker(e) {
        this.$refs.map.mapObject.setZoomAround([e.latlng.lat, e.latlng.lng], 16);
    }    
},
</script>
```
l-map에 ref id 지정을 합니다. 그리고 l-marker에는 click 이벤트를 정의합니다. 그리고 methods에 setZoomAround 기능을 사용합니다. setZoomAround는 leaflet의 기본 함수로 vue-leaflet에는 `$refs`로 불러와서 사용할 수 있습니다. ref는 javascript에서 자식 요소에 직접 접근하기 위해 사용합니다. [공식문서](https://vue2-leaflet.netlify.app/quickstart/#accessing-leaflet-api)에서 leaflet의 기능을 vue-leaflet에서 사용하고 싶으면 ref로 정의하여 접근하라고 나와있습니다. 이렇게 하면 마커를 눌렀을 때 누른 좌표를 가져와서 16 줌 레벨로 당겨줍니다.

클러스터링 마커라는 기술도 있는데요. 한번 적용해보시면 좋을 것 같습니다.