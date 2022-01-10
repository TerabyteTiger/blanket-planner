<template>
  <div>
    <form @action.prevent="">
      <label for="zipcode">Zip:</label>
      <input type="number" name="zipcode" id="zipcode" min="0" v-model="zip" />
      <label for="year">Year:</label>
      <input type="number" name="year" id="year" step="1" v-model="year" />
      <label for="colors">How many colors of yarn will you be using?</label>
      <input
        type="number"
        name="colors"
        min="1"
        step="1"
        id="colors"
        v-model="colors"
      />
      <button @click.prevent="process()">Get Info</button>
    </form>
    <p>min: {{ min }} max: {{ max }}</p>
    <pre v-if="!loading">
      {{ data }}
    </pre>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  data() {
    return {
      loading: true,
      data: {},
      zip: 18014,
      colors: 2,
      year: 2020,
      units: 'us',
      min: 9999999,
      max: -9999999,
    }
  },
  mounted() {
    const today = new Date()
    this.year = today.getFullYear() - 1
    this.process()
  },
  methods: {
    process() {
      this.getData()
      this.findMinMax()
    },
    async getData() {
      this.loading = true

      let tempData = await this.$axios.$get(
        `https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/weatherdata/history?&aggregateHours=24&startDateTime=${this.year}-01-01T00:00:00&endDateTime=${this.year}-06-30T23:59:59&unitGroup=${this.units}&contentType=json&dayStartTime=0:0:00&dayEndTime=0:0:00&location=${this.zip}&key=D3U5HA6U7J98KUEDMWXMQH29A`
      )

      this.data = { ...tempData.locations[this.zip].values }

      tempData = await this.$axios.$get(
        `https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/weatherdata/history?&aggregateHours=24&startDateTime=${this.year}-07-01T00:00:00&endDateTime=${this.year}-12-31T23:59:59&unitGroup=${this.units}&contentType=json&dayStartTime=0:0:00&dayEndTime=0:0:00&location=${this.zip}&key=D3U5HA6U7J98KUEDMWXMQH29A`
      )

      this.loading = false
    },

    hoursDiff(endTime, startTime) {
      return (endTime.getTime() - startTime.getTime()) / 3600000
    },

    findMinMax() {
      for (let i = 0; i < this.data.length; i++) {
        if (this.data[i].temp < this.min) {
          this.min = this.data[i].temp
        }
        if (this.data[i].temp > this.max) {
          this.max = this.data[i].temp
        }
      }
    },
  },
}
</script>
