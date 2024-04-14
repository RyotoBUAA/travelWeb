<template>
  <div>
    <el-button :loading="loading" type="primary" @click.native.prevent="generate">推荐地点</el-button>
    <el-button :loading="loading" type="primary" @click.native.prevent="reset">重置</el-button>
    <div id="container" />
  </div>
</template>

<script>
import AMapLoader from '@amap/amap-jsapi-loader'
import Platform from '../example/components/Dropdown/Platform.vue'
export default {
  name: 'Map',
  data() {
    return {
      selected: [],
      pos: [],
      recommend: [],
      lines: []
    }
  },
  mounted() {
    this.initAMap()
  },
  unmounted() {
    this.map?.destroy()
  },
  methods: {
    initAMap() {
      window._AMapSecurityConfig = {
        securityJsCode: '0d37e1f29749bb678758012e06c919d1'
      }
      AMapLoader.load({
        key: 'ae61507fe14e303c0b6e8fb0ecaea2ff', // 申请好的Web端开发者Key，首次调用 load 时必填
        version: '2.0', // 指定要加载的 JSAPI 的版本，缺省时默认为 1.4.15
        plugins: ['AMap.Scale', 'AMap.PlaceSearch', 'AMap.AutoComplete', 'AMap.ToolBar', 'AMap.MouseTool', 'AMap.Marker'] // 需要使用的的插件列表，如比例尺'AMap.Scale'，支持添加多个如：['...','...']
      })
        .then((AMap) => {
          this.map = new AMap.Map('container', {
            // 设置地图容器id
            viewMode: '3D', // 是否为3D地图模式
            zoom: 11.5, // 初始化地图级别
            center: [116.7, 39.90923] // 初始化地图中心点位置
          })
          this.map.on('click', (e) => {
            const marker = new AMap.Marker({
              position: new AMap.LngLat(e.lnglat.lng, e.lnglat.lat), // 经纬度对象，也可以是经纬度构成的一维数组[116.39, 39.9]
              title: ''
            })
            marker.setMap(this.map)
            marker.setIcon('//a.amap.com/jsapi_demos/static/demo-center/icons/poi-marker-red.png')
            if (this.recommend.length != 0) {
              this.lines[this.lines.length - 1].setMap(null)
              this.lines[this.lines.length - 1] = null
              this.lines.pop()
              this.recommend.forEach(marker => {
                marker.setMap(null)
                marker = null
              })
              this.recommend = []
            }
            this.selected.push(marker)
            var nn = this.pos.length
            if (nn > 0) {
              var polyline = new AMap.Polyline({
                path: [new AMap.LngLat(this.pos[nn - 1][0], this.pos[nn - 1][1]), new AMap.LngLat(e.lnglat.lng, e.lnglat.lat)],
                strokeWeight: 4, // 线条宽度
                strokeColor: 'red', // 线条颜色
                lineJoin: 'round' // 折线拐点连接处样式
              })
              polyline.setMap(this.map)
              this.lines.push(polyline)
            }
            this.pos.push([e.lnglat.lng, e.lnglat.lat])
          })
        })
        .catch((e) => {
          console.log(e)
        })
    },
    generate() {
      var latsum = 0
      var lngsum = 0
      this.pos.forEach(p => {
        lngsum += p[0]
        latsum += p[1]
      })
      if (this.recommend.length == 0) {
        var xx = lngsum / this.pos.length + Math.random() * 0.03
        var yy = latsum / this.pos.length + Math.random() * 0.03
        const marker = new AMap.Marker({
          position: new AMap.LngLat(xx, yy), // 经纬度对象，也可以是经纬度构成的一维数组[116.39, 39.9]
          title: '推荐地点'
        })
        var nn = this.pos.length
        if (nn > 0) {
          var polyline = new AMap.Polyline({
            path: [new AMap.LngLat(this.pos[nn - 1][0], this.pos[nn - 1][1]), new AMap.LngLat(xx, yy)],
            strokeWeight: 4, // 线条宽度
            strokeColor: 'red', // 线条颜色
            lineJoin: 'round', // 折线拐点连接处样式
            strokeStyle: 'dashed'
          })
          polyline.setMap(this.map)
          this.lines.push(polyline)
        }
        marker.setMap(this.map)
        marker.setIcon('//a.amap.com/jsapi_demos/static/demo-center/icons/poi-marker-red.png')
        this.recommend.push(marker)
      }
    },
    reset() {
      this.selected.forEach(marker => {
        marker.setMap(null)
        marker = null
      })
      this.recommend.forEach(marker => {
        marker.setMap(null)
        marker = null
      })
      this.lines.forEach(polyline => {
        polyline.setMap(null)
        polyline = null
      })
      this.selected = []
      this.pos = []
      this.recommend = []
    }
  }
}
</script>

<style scoped>
#container {
  width: 100%;
  height: 800px;
}
</style>
