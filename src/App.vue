<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
     <div>
      <h3>Import XLSX</h3>
      <input type="file" @change="previewFiles" />
      <h3>Table </h3>
        {{ parcels }}
      <table border="1">
        <thead>
          <tr>
            <td>Name</td>
            <td>Mobile</td>
            <td>Address</td>
            <td>Price</td>
            <td>District</td>
            <td>Thana</td>
            <td>Area</td>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(parcel, index) in parcels" :key="index">
            <td><input type="text" v-model="parcel.name"></td>
            <td> <input type="number" v-model="parcel.mobile"></td>
            <td><textarea v-model="parcel.address"></textarea> </td>
            <td> <input type="number" v-model="parcel.price"> </td>
            <td>
              <select v-model="parcel.district_id" @change="changeDistrict(index, parcel.district_id)">
                <option v-for="item in districts" :key="item.id" :value="item.id">{{ item.text }} </option>
              </select>
            </td>
            <td>
              <select v-model="parcel.thana_id" @change="changeThana(index, parcel.thana_id)">
                <option v-for="item in parcel.thanaList" :key="item.id" :value="item.id">{{ item.text }} </option>
              </select>
            </td>
            <td>
              <select v-model="parcel.area_id">
                <option v-for="item in parcel.areaList" :key="item.id" :value="item.id">{{ item.text }} </option>
              </select>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import * as XLSX from "xlsx";

export default {
  data() {
    return {
      file: null,
      parcels: [],
      districts: [
        { id: 1, text: 'Dhaka'},
        { id: 2, text: 'Comilla'}
      ],
      thanas: [
        { id: 1, district_id: 1, text: 'Mirpur'},
        { id: 2, district_id: 1, text: 'Mohakhali'},
        { id: 3, district_id: 2, text: 'Chandina'},
        { id: 4, district_id: 2, text: 'Barura'}
      ],
      areas: [
        { id: 1, thana_id: 1, text: 'Mirpur-10'},
        { id: 2, thana_id: 1, text: 'Mirpur-11'},
        { id: 3, thana_id: 1, text: 'Mirpur-6'},
        { id: 4, thana_id: 1, text: 'Mirpur-12'},

        { id: 5, thana_id: 2, text: 'DOHS'},
        { id: 6, thana_id: 2, text: 'Chairman Bari'},
        { id: 7, thana_id: 2, text: 'Kachabazar'},
        { id: 8, thana_id: 2, text: 'Arjatpara'},

        { id: 9, thana_id: 3, text: 'Barkait'},
        { id: 10, thana_id: 3, text: 'Maijhkhar'},
        { id: 11, thana_id: 3, text: 'Madhaiya'},
        { id: 12, thana_id: 3, text: 'khusbash'}
      ],
      thanaList: [],
      areaList: []
    };
  },
  methods: {
    previewFiles(event) {
      this.file = event.target.files ? event.target.files[0] : null;
      if (this.file) {
        const reader = new FileReader();

        reader.onload = (e) => {
          /* Parse data */
          const bstr = e.target.result;
          const wb = XLSX.read(bstr, { type: 'binary' });
          /* Get first worksheet */
          const wsname = wb.SheetNames[0];
          const ws = wb.Sheets[wsname];
          /* Convert array of arrays */
          const data = XLSX.utils.sheet_to_json(ws, { header: 1 });
          console.log('data = ', data)

          if (data.length > 1) {
            this.parcels = data.slice(1);
          } else {
            this.parcels = [];
          }

          this.parcels = this.parcels.map((item) => {
            return {
              name: item[0],
              mobile: item[1],
              address: item[2],
              price: item[3],
              district_id: 0,
              thana_id: 0,
              area_id: 0,
              thanaList: [],
              areaList: []
            };
          });

        }

        reader.readAsBinaryString(this.file);
      }
    },
    changeDistrict (index, districtId) {
      const thanas = this.thanas.filter(item => item.district_id === districtId)
      this.parcels[index].thanaList = thanas.length > 0 ? thanas : [];
      this.parcels[index].thana_id = 0;
    },
    changeThana (index, thanaId) {
      console.log('index = ', index, ' thana id = ', thanaId)
      const areas = this.areas.filter(item => item.thana_id === thanaId)
      console.log(areas)
      this.parcels[index].areaList = areas.length > 0 ? areas : [];
      this.parcels[index].area_id = 0;
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
