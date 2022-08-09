<template>
  <div id="container">
    <slot></slot>
  </div>
</template>

<script>
import Vue from 'vue'
export default {
  data () {
    return {
      map: '',
      // arr: [{ lat: 116.405285, long: 39.905989 }, { lat: 116.405285, long: 39.909689 }, { lat: 116.405285, long: 39.915989 }, { lat: 116.405285, long: 39.908989 }]
    }
  },
  provide: function () {
    return {
      removeOlver: this.removeOlver,
      addOverlay: this.addOverlay,
    }
  },
  components: {
  },
  mounted () {
    Vue.prototype.$map = new AMap.Map('container', {
      zoom: 15,//级别
      center: [116.397428, 39.90923],//中心点坐标
      resizeEnable: true,
      averageCenter: true
      // mapStyle: 'amap://styles/1938caab0264493fa2a847243b88e511',
    });
    this.map = Vue.prototype.$map
    //  = this.map
    // this.createpolyline()
    // this.createCircle()
    // this.createMarker()
    // this.createDefinite()
    // this.createMassMarks()
    this.createMarkerClusterer()

  },
  methods: {
    removeOlver (overlay) {
      this.$nextTick(() => {
        if (overlay) {
          this.map.remove(overlay)
        }
      })
    },
    addOverlay (overlay) {
      this.$nextTick(() => {
        // console.log(this.map);
        this.map && this.map.add(overlay)
      })
    },
    delOne () {
      // 删除了数组的元素，但是没用，需要调用地图的清除标记的方法
      // this.arr.pop()
    },
    // 海量点聚合，10w数量以下
    createMarkerClusterer () {
      let _this = this
      let markers = []
      // 利用styles属性修改点聚合的图标样式,这个是聚合之后的显示效果，在这里定义
      var styles = [{
        url: "https://a.amap.com/jsapi_demos/static/images/blue.png",
        size: new AMap.Size(32, 32),
        offset: new AMap.Pixel(-16, -16)
      }, {
        url: "https://a.amap.com/jsapi_demos/static/images/green.png",
        size: new AMap.Size(32, 32),
        offset: new AMap.Pixel(-16, -16)
      }, {
        url: "https://a.amap.com/jsapi_demos/static/images/orange.png",
        size: new AMap.Size(36, 36),
        offset: new AMap.Pixel(-18, -18)
      }, {
        url: "https://a.amap.com/jsapi_demos/static/images/red.png",
        size: new AMap.Size(48, 48),
        offset: new AMap.Pixel(-24, -24)
      }, {
        url: "https://a.amap.com/jsapi_demos/static/images/darkRed.png",
        size: new AMap.Size(48, 48),
        offset: new AMap.Pixel(-24, -24)
      }];
      var points = [{
        lnglat: [116.405285, 39.904989], //点标记位置
        name: 'beijing',
        id: 1,
      }, {
        lnglat: [116.405285, 39.905989], //点标记位置
        name: 'beijing',
        id: 1,
      }, {
        lnglat: [116.405285, 39.909689], //点标记位置
        name: 'beijing',
        id: 1,
      }, {
        lnglat: [116.605285, 39.705389], //点标记位置
        name: 'beijing',
        id: 1,
      }, {
        lnglat: [116.505285, 39.604989], //点标记位置
        name: 'beijing',
        id: 1,
      }, {
        lnglat: [116.505285, 39.504989], //点标记位置
        name: 'beijing',
        id: 1,
      }, {
        lnglat: [116.505285, 39.605989], //点标记位置
        name: 'beijing',
        id: 1,
      }, {
        lnglat: [116.505285, 39.607989], //点标记位置
        name: 'beijing',
        id: 1,
      }, {
        lnglat: [116.505285, 39.609989], //点标记位置
        name: 'beijing',
        id: 1,
      }, {
        lnglat: [116.505285, 39.601989], //点标记位置
        name: 'beijing',
        id: 1,
      }];
      for (var i = 0; i < points.length; i += 1) {
        // 这里是点击到无法聚合，到单个点的时候展示的效果
        let singerMarker = new AMap.Marker({
          position: points[i]['lnglat'],
          content: '<div style="background-color: hsla(180, 100%, 50%, 0.7); height: 40px; width: 40px; border: 1px solid hsl(180, 100%, 40%); border-radius: 20px; box-shadow: hsl(180, 100%, 50%) 0px 0px 1px;">123456</div>',
          offset: new AMap.Pixel(-15, -15)
        })
        // 给marker增加click事件
        singerMarker.on('click', _this.clickMarker)
        markers.push(singerMarker)
      }
      //添加聚合组件
      _this.map.plugin(["AMap.MarkerClusterer"], function () {
        new AMap.MarkerClusterer(
          _this.map,     // 地图实例
          markers,    // 海量点组成的数组
          {
            styles: styles,
            gridSize: 80
          }
        );
      });

    },
    clickMarker (e) {
      console.log(e);
      this.$message.success('你点击我了')
    },
    // 海量点标记 1w数量以下 ，MassMarks,按照官网的例子会报错，不知道为啥.......这个海量点标记不会聚合点，只是会把所有的点都标记出来
    createMassMarks () {
      let _this = this
      var styleObject = {
        url: '//vdata.amap.com/icons/b18/1/2.png',  // 图标地址
        size: new AMap.Size(11, 11),      // 图标大小
        anchor: new AMap.Pixel(5, 5) // 图标显示位置偏移量，基准点为图标左上角
      }

      var massMarks = new AMap.MassMarks({
        zIndex: 5,  // 海量点图层叠加的顺序
        zooms: [3, 19],  // 在指定地图缩放级别范围内展示海量点图层
        style: styleObject  // 设置样式对象
      });
      var data = [{
        lnglat: [116.405285, 39.904989], //点标记位置
        name: 'beijing',
        id: 1
      }];

      massMarks.setData(data);
      // 将海量点添加至地图实例
      massMarks.setMap(_this.map);
    },
    // 放大缩小不会改变覆盖物的大小,覆盖物是多大就多大
    createDefinite () {
      var content = '<div class="marker-route marker-marker-bus-from">132456</div>';

      var marker = new AMap.Marker({
        content: content,  // 自定义点标记覆盖物内容
        position: [116.397428, 39.90923], // 基点位置
        // offset: new AMap.Pixel(-17, -42) // 相对于基点的偏移位置
      });

      this.map.add(marker);
    },
    // 创建Marker
    createMarker () {
      var marker = new AMap.Marker({
        position: new AMap.LngLat(116.39, 39.9),   // 经纬度对象，也可以是经纬度构成的一维数组[116.39, 39.9]
        title: '北京'
      });

      // 将创建的点标记添加到已有的地图实例：
      this.map.add(marker);
    },
    // 创建折线
    createpolyline () {
      // 折线的节点坐标数组，每个元素为 AMap.LngLat 对象
      var path = [
        new AMap.LngLat(116.397428, 39.90923),
        new AMap.LngLat(117.382122, 38.901176),
        new AMap.LngLat(116.387271, 37.912501),
        new AMap.LngLat(116.398258, 39.90923)
      ];

      // 创建折线实例
      var polyline = new AMap.Polyline({
        path: path,
        borderWeight: 6, // 线条宽度，默认为 1
        strokeColor: 'red', // 线条颜色
        lineJoin: 'round' // 折线拐点连接处样式
      });

      // 将折线添加至地图实例
      this.map.add(polyline);
    },
    // 创建矢量圆形
    createCircle () {
      // 构造点标记
      var marker = new AMap.Marker({
        icon: "https://webapi.amap.com/theme/v1.3/markers/n/mark_b.png",
        position: [116.405467, 39.907761]
      });
      // 构造矢量圆形
      var circle = new AMap.Circle({
        center: new AMap.LngLat("116.403322", "39.920255"), // 圆心位置
        radius: 1000,  //半径
        strokeColor: "#2c4f78",  //线颜色
        strokeOpacity: 1,  //线透明度
        strokeWeight: 3,  //线粗细度
        fillColor: "#2c4f78",  //填充颜色
        fillOpacity: 0.5 //填充透明度
      });
      // 将以上覆盖物添加到地图上
      // 单独将点标记添加到地图上
      this.map.add(marker);
      // add方法可以传入一个覆盖物数组，将点标记和矢量圆同时添加到地图上
      this.map.add([marker, circle]);
      // 获取到所有的覆盖物
      let a = this.map.getAllOverlays();
      //  删除覆盖物
      this.map.remove(a[0]);
      console.log(a);
    },
  }
}
</script>

<style lang="less" scoped>
#container {
  width: 100%;
  height: 800px;
}

::v-deep .marker-route {
  width: 60px;
  height: 30px;
  text-align: center;
  line-height: 30px;
  background-color: red;
}
</style>