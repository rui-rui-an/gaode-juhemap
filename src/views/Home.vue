<template>
  <div class="home">
    <Amap>
      <!-- <RouteMarker :list="list" @elementClick="elementClick"></RouteMarker>
      <RoutePath :list="list"></RoutePath>
      <InfoWindow :show.sync="showInfoWindow" :info="infoWindowContent"></InfoWindow> -->
    </Amap>
  </div>
</template>

<script>
import Amap from '@/components/aMap/map.vue'
import RouteMarker from '@/components/router-marker'
import RoutePath from '@/components/router-path'
import InfoWindow from './component/InfoWindow.vue'
export default {
  components:{
    Amap,
    RouteMarker,
    RoutePath,
    InfoWindow
  },
  data(){
    return{
      // [{lat:116.405285,long:39.905989},{lat:116.405285,long:39.909689},{lat:116.405285,long:39.915989},{lat:116.405285,long:39.908989}]
      list:[{
        location:[116.405285,39.905989],
        title:'123'
      },{
        location:[116.415585,39.909689],
        title:'4568'
      },{
        location:[116.409385,39.908989],
        title:'789'
      }],
      showInfoWindow:false,
      infoWindowContent:''
    }
  },
  methods: {
    elementClick(info){
      // console.log(info);
      this.infoWindowContent = info
      this.showInfoWindow = true
    },
  }
}
</script>

<style lang="less" scoped>
.home {
}
</style>