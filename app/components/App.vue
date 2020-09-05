<template>
  <Page>
    <ActionBar title="4G-Gym"/>
    <FlexboxLayout flexDirection="column" justifyContent="space-around">
      <!-- START/STOP -->
      <Button class="recording start" height="50" text="Start Recording" @tap="start" v-if="!startRecording"/>
      <Button class="recording stop" height="50" text="Stop Recording" @tap="stop" v-if="startRecording" />

      <!-- RESULTS TABLE -->
      <ScrollView orientation="vertical" height="80%" class="scroll">
        <GridLayout :rows="genRows()" columns="*, *, *, *" class="results-table">
          <!-- HEADERS -->
          <label row="0" col="0" text="RSSI" class="table-header" />
          <label row="0" col="1" text="LAT" class="table-header" />
          <label row="0" col="2" text="LON" class="table-header" />
          <label row="0" col="3" text="ALT" class="table-header" />

          <!-- CONTENT -->
          <label class="table-data" :row="index + 1" col="0" v-for="(record, index) in records" :key="index" :text="record['rssi']" />
          <label class="table-data" :row="index + 1" col="1" v-for="(record, index) in records" :key="index" :text="record['lat']" />
          <label class="table-data" :row="index + 1" col="2" v-for="(record, index) in records" :key="index" :text="record['lon']" />
          <label class="table-data" :row="index + 1" col="3" v-for="(record, index) in records" :key="index" :text="record['alt']" />

        </GridLayout>
      </ScrollView>

      <!-- ACTIONS -->
      <FlexboxLayout justifyContent="space-around" class="actions-container">
        <Button class="actions" text="Clear" width="33%" @tap="clear" />
        <Label :text="records.length" width="33%" fontSize="50px" textAlign="center" />
        <Button class="actions" text="Export" width="33%" @tap="exportData"/>
      </FlexboxLayout>
    </FlexboxLayout>
  </Page>
</template>

<script >
const application = require('application');
const permissions = require('nativescript-permissions');
const geolocation = require("nativescript-geolocation");
const { Accuracy } = require("tns-core-modules/ui/enums");

// const fileSystem = require("tns-core-modules/file-system");
import { knownFolders, path, File, Folder } from "tns-core-modules/file-system";

export default {
  data: () => {
    return {
      startRecording: false,
      records: [],
      interval: null,
      folder: null,
      file: null
    }
  },
  methods: {
    toObject(str, delimeter) {
      let data = str.filter(s => s.length > 0);
      data = data.reduce((acc, d) => {
        const [key, value] = d.split(delimeter);
        return {
          ...acc,
          ...{[key]: value}
        }
      }, {});
      return data;
    },
    genRows() {
      const rows = this.records.reduce(acc => `${acc}, auto`, "auto");
      return rows;
    },
    clear() {
      this.records = [];
    },
    start() {
        this.startRecording = true;
        this.interval = setInterval(this.record, 1000);
    },
    stop() {
      this.startRecording = false;
      clearInterval(this.interval);
    },
    async exportData() { 
      try {
        await permissions.requestPermission(android.Manifest.permission.ACCESS_NETWORK_STATE, "network state");
        await permissions.requestPermission(android.Manifest.permission.ACCESS_COARSE_LOCATION, "location");
        await permissions.requestPermission(android.Manifest.permission.READ_PHONE_STATE, "phone state");
        await permissions.requestPermissions(android.Manifest.permission.INTERNET, "internet");
        await permissions.requestPermission(android.Manifest.permission.READ_EXTERNAL_STORAGE, "read external storage");
        await permissions.requestPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE, "write external storage");
        await geolocation.enableLocationRequest();
      } 
      catch(error) {
        console.error(error)
      };

      const records = JSON.stringify(this.records);

      let documentsFolder = knownFolders.documents();
      let myPath = path.join(documentsFolder.path, "records.txt");
      let file = File.fromPath(myPath);

      // Writing text to the file.
      file.writeText(records)
          .then(result => {
              // Succeeded writing to the file.
              console.dir(file.path);
              file.readText().then(res => {
                  // Succeeded read from file.
                  console.log("File content: " + res);
              });
          }).catch(err => {
              console.log(err.stack);
          });
    },
    async record() {
      try {
        await permissions.requestPermission(android.Manifest.permission.ACCESS_NETWORK_STATE, "network state");
        await permissions.requestPermission(android.Manifest.permission.ACCESS_COARSE_LOCATION, "location");
        await permissions.requestPermission(android.Manifest.permission.READ_PHONE_STATE, "phone state");
        await permissions.requestPermission(android.Manifest.permission.READ_EXTERNAL_STORAGE, "read external storage");
        await permissions.requestPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE, "write external storage");
        await geolocation.enableLocationRequest();
      } 
      catch(error) {
        console.error(error)
      };

      try {
        let manager = application.android.context.getSystemService(android.content.Context.TELEPHONY_SERVICE);

        // get mCi, mPci
        const cid = manager.getCellLocation().getCid();

        // get rssi in asu
        let cellSignalStrength = manager.getAllCellInfo().get(0).getCellSignalStrength();
        const rssi= cellSignalStrength.getAsuLevel();
        const rsrq = cellSignalStrength.getRsrq();
        const rssnr = cellSignalStrength.getRssnr();

        geolocation
          .getCurrentLocation({
              desiredAccuracy: Accuracy.high,
              maximumAge: 5000,
              timeout: 20000
          })
          .then(res => {
              console.dir(res);
              let data = {lat: res.latitude, lon: res.longitude, alt: res.altitude, cid, rssi, rsrq, rssnr}
              
              this.records = [
                ...this.records,
                data
              ];
          });
      } catch(error) {
        console.error(error);
      }      
     
    },
  },
}
</script>

<style scoped>
/* https://docs.nativescript.org/ui/styling */

ActionBar {
  background-color: #4f617d;
  color: #ffffff;
}

Label {
  text-align: center;
}

.recording {
  background-color: white;
    margin-right: 300px;
  margin-left: 300px;
  margin-top: 50px;
}

.start {
  border-color: green;
  border-width: 5px;
}

.stop {
  border-color: red;
  border-width: 5px;
}

.results-table {
  margin-top: 50px;
  margin-bottom: 50px;
  padding-top: 50px;
  padding-bottom: 50px;
  background-color: rgba(0, 0, 0, 0.05);
}

.table-header {
  color: black; 
  font-size: 20px;
  text-align: center;
  font-weight: 800;
}

.table-data {
  text-align: center;
  font-size: 20px;
}

.actions-container {
  margin-bottom: 50px;
}

.actions {
  background-color: white;
  border-width: 5px;
  border-color: #4f617d;
  margin-left: 20px;
  margin-right: 20px;
}
</style>
