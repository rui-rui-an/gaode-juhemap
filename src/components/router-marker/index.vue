<template>
  <div>
    <!-- 区域 弹出窗体 -->
    <MapPopup :position="MapPopupData.position" :mapShow="MapPopupData.show" @close="MapPopupClose">
      <div class="content">
        <h5 class="clickpop-h">{{ content.title }}</h5>
        <ul>
          <!-- <li v-for="(item, index) in content.timeList" :key="index">
            {{ item }}
          </li> -->
          <li>{{ content.title }}</li>
        </ul>
      </div>
    </MapPopup>
  </div>
</template>

<script>
// import infoWindowMixin from '@/mixins/infoWindow'
// import dayjs from '@/utils/filters'
// import { mapState, mapMutations } from 'vuex'
import MapPopup from '@/components/MapPopup'

export default {
  components: {
    MapPopup
  },
  // mixins: [infoWindowMixin],
  inject: ['addOverlay', 'removeOlver'],
  props: {
    layerClass: {
      type: String,
      default: 'count-marker'
    },
    list: {
      type: Array,
      default: () => []
    }
  },
  data () {
    return {
      markerList: [],
      // 弹出窗体图层数据
      MapPopupData: {
        position: [], // 弹窗中心点 Array[array]， 必须
        show: false // 弹窗显隐 Boolean，必须，默认为 true
      },
      content: {}
    }
  },
  computed: {
    // ...mapState('Common', {
    //   cMap: 'cMap'
    // })
  },
  watch: {
    list: function (val) {
      // 清除之前图层
      this.markerList.length > 0 && this.removeOlver(this.markerList)
      // 渲染
      this.$nextTick(() => this.renderMarker())
    }
  },
  mounted () {
    this.renderMarker()
  },
  methods: {
    // 关闭区域弹出窗体事件
    MapPopupClose(e) {
      this.MapPopupData.show = false
    },
    renderMarker () {
      this.markerList = []
      const len = this.list.length
      const markerIconMap = {
        start: 'icon-router-start',
        by: 'icon-router-by',
        end: 'icon-router-end'
      }

      // 当只有一个点时
      if (len === 1) {
        this.generateMarker(this.list[0], markerIconMap['start'])
      } else {
        this.list.forEach((marker, idx) => {
          const key = idx === 0 ? 'start' : idx == len - 1 ? 'end' : 'by'
          this.generateMarker(marker, markerIconMap[key])
        })
      }

      // 渲染
      this.markerList.forEach(marker => this.addOverlay(marker))
    },
    generateMarker (item, cls) {
      // console.log(cls);
      const { location, title, timeList } = item
      const curCotent = {
        title: item.title,
        // timeList: item.timeList
      }
      const overlay = new AMap.Marker({
        offset: new AMap.Pixel(-16, -34),
        position: location,
        content: `<div class="${cls} icon-router-marker"></div>`,
        title: title,
        extData: {
          ...item
        }
      })

      overlay.on('click', e => {
        // https://www.cnblogs.com/xiaomg/articles/10432907.html
        let Cotent = JSON.parse(JSON.stringify(e.target.getExtData()));
        // console.log(Cotent);
        this.$emit('elementClick', Cotent)
        this.content = curCotent
        this.MapPopupData.show = true
        this.MapPopupData.position = location
      })

      this.markerList.push(overlay)
    }
  }
}
</script>

<style>
.icon-router-marker {
  cursor: pointer;
  width: 32px;
  height: 32px;
  background-color: red;
  border-radius: 50%;
  background-repeat: round;
  /* position: relative;
  z-index: 11111; */
}
.icon-router-start {
  background: url(~@/assets/icon-route-start.png);
}
.icon-router-by {
  background: url(~@/assets/icon-route-by.png);
}
.icon-router-end {
  background: url(~@/assets/icon-route-end.png);
}
.clickpop-h {
  margin-top: 0;
  margin-bottom: 6px;
}
</style>
