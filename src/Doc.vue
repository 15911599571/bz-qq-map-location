<style lang=less>
  .map {
    width: 100%;
    height: 500px;
  }
</style>

<template>
  <div>
    <doc name="bz-bottom-loader"
      desc="拉到底部加载"
      parm_desc="滚动到底部以后，加载更多内容(dom没有屏幕长，是无法触发的，以后修正)"
      :parms="parms"
      :code="code"
      >
    </doc>
      <q-map :map.sync="map" :config_map="configMap" class="map"></q-map>
      <q-map-location :loc.sync="loc" ></q-map-location>
      <img @click="toLocation" id="location" class="ui image" src="./assets/icon_location.png"><img>
  </div>
</template>

<script>
  import $ from 'jQuery'
  import 'bz-semantic-ui-card'
  import 'bz-semantic-ui-grid'
  import QMapLocation from './Bz'
  import QMap from 'bz-qq-map'

  import Doc from 'bz-doc'
  export default {
    components: {
      QMap,
      QMapLocation,
      Doc
    },
    route: {
      deactivate: function (transition) {
        this.$broadcast('unbind-scroll')
        console.log('解除了scroll绑定')
        transition.next()
      }
    },
    data: function () {
      return {
        map: {},
        marker: null,
        loc: null,
        name: 'QMapLocation',
        desc: '封装了qq map的定位插件，能取到当前位置',
        parms: [
          {parm: 'loc', desc: '当前位置，要开启 two way'}
        ],
        code: `<q-map-location :loc.sync="loc" ></q-map-location>`
      }
    },
    methods: {
      toLocation: function () {
        if (this.loc) {
          let position = new window.qq.maps.LatLng(this.loc.lat, this.loc.lng)
          this.map.panTo(position)
          this.setMark(position)
        } else {
          console.log('定位失败!')
          console.log(this.loc)
        }
      },
      setMark: function (position) {
        if (this.marker) {
          this.marker.setMap(null)
          this.marker = null
        }
        this.marker = new window.qq.maps.Marker(
          {
            position: position,
            map: this.map,
            animation: window.qq.maps.MarkerAnimation.DROP
          }
        )
      },
      configMap: function (map) {
        // 加入按钮
        map.controls[window.qq.maps.ControlPosition.BOTTOM_RIGHT].push($('#location')[0])
      }
    }
  }
</script>
