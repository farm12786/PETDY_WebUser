<template>
  <div>
    <pagebar></pagebar>
    <toolbar @val="getbutype2"></toolbar>
    <v-container grid-list-xs>
      <GmapMap
        id="map"
        :center="currentPositions"
        :zoom="15"
        map-type-id="terrain"
        min-width="100"
        min-height="100"
        style="min-width: 300px; min-height: 400px"

      >
        <div v-if="maptype === 'bu'">
        <GmapCustomMarker
          v-for="(m, index) in markers"
          :key="index"
          :marker="m.position"
        >
          <v-img @click="onClickbumap(m.id)" src="../../assets/icon/marker.png" height="50" width="50" >
            <v-avatar size="33" color="primary" rounded="circle" style="margin-left:16%; margin-top:7%">
              <v-img :src="
                        'https://s3gw.inet.co.th:8082/static/media/' +
                        m.image
                      "> </v-img>
            </v-avatar>
          </v-img>
        </GmapCustomMarker>
        </div>

        <div v-if="maptype === 'pet'">
        <GmapCustomMarker
          v-for="(m, index) in markers"
          :key="index"
          :marker="m.position"
        >
          <v-img @click="onClickbumap(m.id)" src="../../assets/icon/marker.png" height="50" width="50" >
            <v-avatar size="33" color="primary" rounded="circle" style="margin-left:16%; margin-top:7%">
              <v-img :src="
                        'https://s3gw.inet.co.th:8082/static/media/' +
                        m.image
                      "> </v-img>
            </v-avatar>
          </v-img>
        </GmapCustomMarker>
        </div>

        <div v-if="maptype === 'pet'">
        <GmapCustomMarker
          v-for="(m, index) in markers"
          :key="index"
          :marker="m.position"
        >
          <v-img @click="onClickpetmap(m.id)" src="../../assets/icon/marker.png" height="50" width="50" >
            <v-avatar size="33" color="primary" rounded="circle" style="margin-left:16%; margin-top:7%">
              <v-img :src="
                        'https://s3gw.inet.co.th:8082/static/media/' +
                        m.image
                      "> </v-img>
            </v-avatar>
          </v-img>
        </GmapCustomMarker>
        </div>
      </GmapMap>

      <v-btn color="success" @click="onClickSelectbumaptype">Business</v-btn>
      <v-btn color="success" @click="onClickSelectpetmaptype">Pet</v-btn>

      <div style="visibility: hidden">
        {{ this.$store.state.butype }}
      </div>
    </v-container>
    <pagefoot />
  </div>
</template>

<script>
import GmapCustomMarker from 'vue2-gmap-custom-marker'
import axios from 'axios'
import toolbar from '@/components/location/toolbar'
import pagebar from '@/components/location/pagebar'
import pagefoot from '@/components/location/footer'

export default {
  name: 'bumap',
  components: {
    toolbar,
    pagebar,
    pagefoot,
    GmapCustomMarker
  },
  data () {
    return {
      buData: [],
      maptype: 'bu',
      butype: 'All',
      currentPositions: { lat: null, lng: null },
      markPoint: { lat: null, lng: null },
      markers: []
    }
  },
  methods: {
    async getMarker (maptype, butype) {
      if (this.maptype === 'bu') {
        if (butype === 'All') {
          const result1 = await axios.post(
            'https://testchat.one.th/petdy/get_business_by_type',
            { bu_type: 'hospital' }
          )
          const result2 = await axios.post(
            'https://testchat.one.th/petdy/get_business_by_type',
            { bu_type: 'cafe' }
          )
          const result3 = await axios.post(
            'https://testchat.one.th/petdy/get_business_by_type',
            { bu_type: 'hotel' }
          )
          const result4 = await axios.post(
            'https://testchat.one.th/petdy/get_business_by_type',
            { bu_type: 'shop' }
          )
          const result5 = await axios.post(
            'https://testchat.one.th/petdy/get_business_by_type',
            { bu_type: 'salon' }
          )
          const result6 = await axios.post(
            'https://testchat.one.th/petdy/get_business_by_type',
            { bu_type: 'farm' }
          )

          result1.data.list_business.forEach((hospital) => {
            this.buData.push(hospital)
          })
          result2.data.list_business.forEach((cafe) => {
            this.buData.push(cafe)
          })
          result3.data.list_business.forEach((hotel) => {
            this.buData.push(hotel)
          })
          result4.data.list_business.forEach((shop) => {
            this.buData.push(shop)
          })
          result5.data.list_business.forEach((salon) => {
            this.buData.push(salon)
          })
          result6.data.list_business.forEach((farm) => {
            this.buData.push(farm)
          })

          this.buData.forEach((marker) => {
            this.markers.push({
              position: {
                lat: marker.latitude,
                lng: marker.longitude
              },
              id: marker.id,
              image: marker.image
            })
          })
        } else {
          const result = await axios.post(
            'https://testchat.one.th/petdy/get_business_by_type',
            { bu_type: butype }
          )

          result.data.list_business.forEach((marker) => {
            this.markers.push({
              position: {
                lat: marker.latitude,
                lng: marker.longitude
              },
              id: marker.id,
              image: marker.image
            })
          })
        }
      } else if (this.maptype === 'pet') {
        const result = await axios.post(
          'https://testchat.one.th/petdy/get_pet_location'
        )

        result.data.list_pet_location.forEach((marker) => {
          this.markers.push({
            position: {
              lat: marker.latitude,
              lng: marker.longitude
            },
            id: marker.id,
            image: marker.pic
          })
        })
      }
    },
    getcurrentPosition () {
      navigator.geolocation.getCurrentPosition(
        (position) => {
          this.currentPositions.lat = position.coords.latitude
          this.currentPositions.lng = position.coords.longitude
          console.log(this.currentPositions)
        },
        (error) => {
          console.log(error.message)
        }
      )
    },
    onClickbumap (buid) {
      this.$router.push({
        name: 'profile',
        query: { id: buid }
      })
      this.$ls.set('buId', JSON.stringify(buid))
    },
    onClickpetmap (petid) {
      this.$router.push({
        name: 'mypetProfile',
        query: { id: petid }
      })
      this.$ls.set('buId', JSON.stringify(petid))
    },
    getbutype2 () {
      this.butype = this.$store.state.butype
    },
    clearMarker () {
      this.markers = []
    },
    onClickSelectbumaptype () {
      this.maptype = 'bu'
    },
    onClickSelectpetmaptype () {
      this.maptype = 'pet'
    }
  },
  mounted () {
    this.getcurrentPosition()
    this.clearMarker()

    // this.getMarker(this.$store.state.butype)
    this.getMarker(this.maptype, this.butype)
  },
  updated () {
    this.butype = this.$store.state.butype
  },
  watch: {
    butype (newVal) {
      console.log(this.butype)
      console.log(newVal)
      this.clearMarker()
      this.getMarker(this.maptype, newVal)
    },
    maptype (newVal) {
      console.log(this.butype)
      console.log(newVal)
      this.clearMarker()
      this.getMarker(newVal, this.butype)
    }

  }
}
</script>

<style lang="scss">
@import "../../assets/style/style.css";
</style>
