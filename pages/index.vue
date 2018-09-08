<template>
  <v-layout column wrap justify-center>
    <v-flex xs6 ma-2>
      <v-card>
        <v-card-title class="headline blue lighten-4">現在地のロケーションを取得</v-card-title>
        <v-card-text>
          <v-list>
            <v-layout row v-for="(point, index) in points" :key="point.index">
              <v-flex xs2 ma-2>
                <v-subheader xs2>地点{{index}}</v-subheader>
              </v-flex>
              <v-flex xs3 ma-2>
                <v-text-field label="経度" single-line v-model="point.latitude"></v-text-field>
              </v-flex>
              <v-flex xs3 ma-2>
                <v-text-field label="緯度" single-line v-model="point.longitude"></v-text-field>
              </v-flex>
              <v-flex xs4 ma-2>
                <v-btn @click="getPosition(index)" small fab>
                  <v-icon>gps_fixed</v-icon>
                </v-btn>
                <v-btn @click="removePoint(index)" small fab>
                  <v-icon>remove_circle</v-icon>
                </v-btn>
              </v-flex>
            </v-layout>
          </v-list>
        </v-card-text>
        <v-card-actions>
          <v-btn @click="calcDistanceAndDirection">
            <v-icon>360</v-icon>距離と角度を計算
          </v-btn>
          <v-spacer></v-spacer>
          <v-btn @click="addPoint">
            <v-icon>add_location</v-icon>addPoint
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-flex>

    <v-flex xs6 ma-2 v-if="lines.length>0">
      <v-card>
        <v-card-title class="headline blue lighten-4">距離計算結果</v-card-title>
        <v-card-tile>
          <table>
            <thead>
              <tr>
                <th>No</th>
                <th>説明</th>
                <th>距離</th>
                <th>角度</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(line,index) in lines" :key="index">
                <td style="text-align:center; padding:0px;">{{index}}</td>
                <td>{{line.descrption}}</td>
                <td>{{line.distance.toFixed(3)}} m</td>
                <td>{{line.direction.toFixed(3)}}°</td>
              </tr>
            </tbody>
          </table>
        </v-card-tile>
      </v-card>
    </v-flex>
  </v-layout>
</template>
<script>
export default {
  data () {
    return {
      points: [
        { 'latitude': 34.803008, 'longitude': 135.628324 },
        { 'latitude': 34.803453, 'longitude': 135.628396 },
        { 'latitude': 34.803497, 'longitude': 135.626272 },
        { 'latitude': 34.802387, 'longitude': 135.626637 },
        { 'latitude': 34.802409, 'longitude': 135.627109 }
      ],
      lines: []
    }
  },
  methods: {
    getPosition (index) {
      navigator.geolocation.getCurrentPosition(
        // 取得成功した場合
        function (position) {
          let point = this.points[index]
          point.latitude = position.coords.latitude
          point.longitude = position.coords.longitude
        },
        // 取得失敗した場合
        function (error) {
          switch (error.code) {
            case 1: // PERMISSION_DENIED
              alert('位置情報の利用が許可されていません')
              break
            case 2: // POSITION_UNAVAILABLE
              alert('現在位置が取得できませんでした')
              break
            case 3: // TIMEOUT
              alert('タイムアウトになりました')
              break
            default:
              alert('その他のエラー(エラーコード:' + error.code + ')')
              break
          }
        }
      )
    },
    addPoint () {
      this.points.push({ 'latitude': null, 'longitude': null })
    },
    removePoint (index) {
      this.points.splice(index, 1)
    },
    calcDistanceAndDirection () {
      this.lines = []
      for (var i = 0; i < this.points.length; i++) {
        let pointA
        let pointB
        let bIndex
        pointA = this.points[i]
        if (i === this.points.length - 1) {
          bIndex = 0
        } else {
          bIndex = i + 1
        }
        pointB = this.points[bIndex]
        this.lines.push([{ 'descrption': '', 'distance': null, 'direction': null }])
        let distance = this.geoDistance(pointA.latitude, pointA.longitude, pointB.latitude, pointB.longitude, 7)
        let direction = this.geoDirection(pointA.latitude, pointA.longitude, pointB.latitude, pointB.longitude)

        this.lines[i].descrption = '地点' + i + ' ⇒ 地点' + bIndex
        this.lines[i].distance = distance
        this.lines[i].direction = direction
      }
    },
    geoDistance (lat1, lng1, lat2, lng2, precision) {
      // 引数precisionは小数点以下の桁数（距離の精度）
      // http://hamasyou.com/blog/2010/09/07/post-2/
      var distance = 0
      if ((Math.abs(lat1 - lat2) < 0.00001) && (Math.abs(lng1 - lng2) < 0.00001)) {
        distance = 0
      } else {
        lat1 = lat1 * Math.PI / 180
        lng1 = lng1 * Math.PI / 180
        lat2 = lat2 * Math.PI / 180
        lng2 = lng2 * Math.PI / 180

        var A = 6378140
        var B = 6356755
        var F = (A - B) / A

        var P1 = Math.atan((B / A) * Math.tan(lat1))
        var P2 = Math.atan((B / A) * Math.tan(lat2))

        var X = Math.acos(Math.sin(P1) * Math.sin(P2) + Math.cos(P1) * Math.cos(P2) * Math.cos(lng1 - lng2))
        var L = (F / 8) * ((Math.sin(X) - X) * Math.pow((Math.sin(P1) + Math.sin(P2)), 2) / Math.pow(Math.cos(X / 2), 2) - (Math.sin(X) - X) * Math.pow(Math.sin(P1) - Math.sin(P2), 2) / Math.pow(Math.sin(X), 2))

        distance = A * (X + L)
        var decimalNo = Math.pow(10, precision)
        distance = Math.round(decimalNo * distance / 1) / decimalNo // kmに変換するときは(1000で割る)
      }
      return distance
    },
    geoDirection (lat1, lng1, lat2, lng2) {
      // 緯度経度 lat1, lng1 の点を出発として、緯度経度 lat2, lng2 への方位
      // 北を０度で右回りの角度０～３６０度
      var Y = Math.cos(lng2 * Math.PI / 180) * Math.sin(lat2 * Math.PI / 180 - lat1 * Math.PI / 180)
      var X = Math.cos(lng1 * Math.PI / 180) * Math.sin(lng2 * Math.PI / 180) - Math.sin(lng1 * Math.PI / 180) * Math.cos(lng2 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180 - lat1 * Math.PI / 180)
      var dirE0 = 180 * Math.atan2(Y, X) / Math.PI // 東向きが０度の方向
      if (dirE0 < 0) {
        dirE0 = dirE0 + 360 // 0～360にする。
      }
      var dirN0 = (dirE0 + 90) % 360 // (dirE0+90)÷360の余りを出力北向きが０度の方向
      return dirN0
    }
  }
}
</script>
<style scoped>
table {
  width: 100%;
  font-size: 14pt;
  border: solid 1px #cccccc;
  border-collapse: collapse;
}
td {
  border: solid 1px #cccccc;
  padding: 2px;
  padding-right: 5%;
  text-align: right;
}
th {
  border: solid 1px #cccccc;
}
</style>
