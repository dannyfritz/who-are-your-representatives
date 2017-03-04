<template>
  <div id="app">
    <h1>Who Are Your Representatives?</h1>
    <h2>Representatives</h2>
    <main>
      <div>
        <ul>
          <li v-for="(official, index) in representatives">
            <h3>{{ official.name }}</h3>
            {{ getOfficeByOfficial(index).name }}
            - {{ official.party }}
          </li>
        </ul>
      </div>
      <h2>Divisions</h2>
      <div>
        <ul>
          <li v-for="division in divisions">
            <h3>{{ division.name }}</h3>
            <div v-for="office in getOfficesByDivision(division)">
              <h4>{{ office.name }}</h4>
              <div v-for="official in getOfficialsByOffice(office)">
                {{ official.name }}
                - {{ official.party }}
              </div>
            </div>
          </li>
        </ul>
      </div>
      <h2>Offices</h2>
      <div>
        <ul>
          <li v-for="office in offices">
            <h3>{{ office.name }}</h3>
            <div v-for="official in getOfficialsByOffice(office)">
              {{ official.name }}
              - {{ official.party }}
            </div>
          </li>
        </ul>
      </div>
    </main>
  </div>
</template>

<script>
import { civicKey } from "../config"
import axios from "axios"

//TODO (danny): Remove address
const address = "1730 W Wellington Chicago, IL"

export default {
  name: 'app',
  data () {
    axios.get(`https://www.googleapis.com/civicinfo/v2/representatives?key=${civicKey}&address=${address}`)
    // axios.get('https://cdn.rawgit.com/dannyfritz/722adb8ef69105f0aa84101fbd3c8099/raw/cebf740c9b1810e2ca35fbbd4781e9ecf204bd79/civicresponse.json')
      .then((response) => {
        this.representatives = response.data.officials
        this.offices = response.data.offices
        this.divisions = response.data.divisions
        console.log(response.data)
      })
      .catch((error) => {
        console.error(error)
      })
    return {
      offices: null,
      divisions: null,
      representatives: null,
    }
  },
  methods: {
    getOfficeByOfficial (officialIndex) {
      return this.offices.find(
          (office) => office.officialIndices.some(
            (index) => index === officialIndex
          )
        )
    },
    getOfficialsByOffice (office) {
      return this.representatives.filter(
          (official, index) => office.officialIndices.includes(index)
        )
    },
    getOfficesByDivision (division) {
      console.log(division)
      if (!division.officeIndices) {
        return []
      }
      return this.offices.filter(
          (office, index) => division.officeIndices.includes(index)
        )
    }
  }
}
</script>

<style>
:root {
  font-size: 16px;
  font-family: 'Open Sans', sans-serif;
}
h1, h2, h3, h4, h5 {
  padding: 0.5rem 0;
}
h1 {
  font-size: 2rem;
}
h2 {
  font-size: 1.75rem;
}
h3 {
  font-size: 1.5rem;
}
h4 {
  font-size: 1.25rem;
}
main {
  padding: 1rem;
}
</style>
