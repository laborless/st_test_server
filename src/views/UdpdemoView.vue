<template>
  <hr>
  <div v-if="$root.session == 0">
    <a><strong>Please Sign In</strong></a>
  </div>
  <div v-else>
    <div>
      <img src="../assets/st-logo-blue-vertical.png"><br>
      <div class="NavMenu">
        <span class="NavItem" @click="setNavItem(0)">🏠Home</span>
        <span class="NavItem" @click="setNavItem(1)">⚡Data</span>
        <span class="NavItem" @click="setNavItem(2)">🛜ST87M01</span>
        <span class="NavItem" @click="setNavItem(3)">⚙️Setting</span>
      </div>
    </div>
    <div class="Wrapper">
      <div v-if="navItem == 1">
        <h1>Rx Sensor Data from ST87M01</h1><br>
        <div class="data-div" v-for="sensorValue in sensorValues" v-bind:key="sensorValue.id">
          <div class="value-container" v-if="sensorValue.imgFilter === 1">
            <img src="../assets/ADC.png">
            <span v-if="sensorValue.changed === 0" style="color:black;">{{sensorValue.name}}: {{sensorValue.value}}</span>
            <span v-else style="color:red;">{{sensorValue.name}}: {{sensorValue.value}}</span>
          </div>
          <div class="value-container" v-else-if="sensorValue.imgFilter === 2">
            <img src="../assets/temperature_sensor.png">
            <span v-if="sensorValue.changed === 0" style="color:black;">{{sensorValue.name}}: {{sensorValue.value}}</span>
            <span v-else style="color:red;">{{sensorValue.name}}: {{sensorValue.value}}</span>
          </div>
          <div class="value-container" v-else-if="sensorValue.imgFilter === 3">
            <img src="../assets/humidty_sensor.png">
            <span v-if="sensorValue.changed === 0" style="color:black;">{{sensorValue.name}}: {{sensorValue.value}}</span>
            <span v-else style="color:red;">{{sensorValue.name}}: {{sensorValue.value}}</span>
          </div>
          <div class="value-container" v-else>
            <img src="../assets/general_sensor.png">
            <span v-if="sensorValue.changed === 0" style="color:black;">{{sensorValue.name}}: {{sensorValue.value}}</span>
            <span v-else style="color:red;">{{sensorValue.name}}: {{sensorValue.value}}</span>
          </div>
        </div>
        <img src="../assets/qr-code.png" style="width:300px; height:auto">
        <br>
        <a>https://193.122.103.95/udpdemo</a>
      </div>
      <div v-else-if="navItem == 2">
        <h1>
          <strong>ST87M01</strong><br>
          <a>Ultra-compact, low-power narrowband (NB-IoT) industrial module series with GNSS capability</a>
        </h1>
        <img src="../assets/ST87M01_Module.png"><br>
        <div class="ST87M01_Desc">
          <div class="ST87M01_Desc_list">
            <a>The ST87M01 is an ultra-compact, ultra low-power, certified NB-IoT and GNSS module series,</a><br>
            <a> featuring multiband data transmission and geolocation in a supercompact form factor, with full ecosystem support.</a>
            <ul>
              <li>LTE, Category NB2, Release 15</li>
              <li>GNSS capability</li>
              <li>Worldwide regional bands coverage</li>
              <li>Single-tone / Multi-tone / Extended TBS and 2 HARQ up to DL: 127 kbps, UL: 159 kbps</li>
              <li>eDRX and PSM support</li>
              <li>Low-power mode ~ 2 uA</li>
              <li>Ultra-compact size</li>
              <li>Embedded IoT internet protocols</li>
              <li>Differential FOTA support</li>
              <li>Up to +23 dBm power output</li>
              <li>Multiple I/F and GPIO</li>
              <li>Optional eSIM GSMA compliant with an additional Secure Element</li>
            </ul>
          </div>
        </div>
        <img src="../assets/ST87M01_EVK.png"><br>
        <div class="ST87M01_Desc">
          <div class="ST87M01_Desc_list">
            <a>The EVKITST87M01-1 evaluation board, based on the ST87M01 module,</a><br>
            <a> is designed to be compatible with the Nucleo hardware environment by Arduino connectors.</a><br>
            <a>The board includes two possible HOST connections to ST87M01 for a PC or associated board.</a>
          </div>
        </div>
        <a href="https://www.st.com/en/wireless-connectivity/st87m01.html" target="_blank" color="#00b0f0">https://www.st.com/en/wireless-connectivity/st87m01.html</a><br>
      </div>
      <div v-else-if="navItem == 3">
        <br>
        <a>Host Address : 193.122.103.95</a><br>
        <a>Refresh Rate : </a>
        <input type="number" min="1" v-model="refreshRate" size=5 placeholder="2"> s<br>
      </div>
      <div v-else>
        <h1>Welcome to <strong>ST NB-IoT</strong> Demo</h1><br>
        <img src="../assets/Home_conns.png">
        <br>
        <img src="../assets/qr-code.png" style="width:400; height:auto;">
        <br>
        <a>https://193.122.103.95/udpdemo</a><br>
        <br><a href="https://www.st.com/en/wireless-connectivity/st87m01.html" target="_blank" color="#00b0f0">https://www.st.com/en/wireless-connectivity/st87m01.html</a><br>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      navItem: 0,
      refreshRate: 2,
      interval: null,
      sensorValues: [{ name: 'Sensor', value: 'Value', imgFilter: 0, changed: 1 }],
      numSensorValue: 1
    }
  },
  mounted () {
    this.updateData()
  },
  unmounted () {
    if (this.interval) {
      clearInterval(this.interval)
      this.interval = null
    }
  },
  methods: {
    async updateData () {
      if (this.navItem !== 1) return
      try {
        const response = await fetch('https://' + window.location.hostname + '/api/udpecho/latest', {
          // mode: 'cors',
          credentials: 'include'
        })
        if (response.status === 200) {
          const resp = await response.json()
          // const resp = [{ id: 57084, time: '2024-01-19 12:29:30.108', type: 'Tx', address: '93.23.19.159', port: '5000', payload: '#DEMO:ADC_CH1_VOLTAGE,0.006,TEMPERATURE,21.787,HUMIDITY,34.313', payload_len: 78 }, { id: 57083, time: '2024-01-19 12:29:30.100', type: 'Rx', address: '93.23.19.159', port: '5000', payload: '#DEMO:ADC_CH1_VOLTAGE [V],0.006,TEMPERATURE [degC],21.787,HUMIDITY [RH],34.313', payload_len: 78 }, { id: 57082, time: '2024-01-19 12:29:20.198', type: 'Rx', address: '93.23.19.159', port: '5000', payload: '#DEMO:ADC_CH1_VOLTAGE [V],0.003,TEMPERATURE [degC],21.769,HUMIDITY [RH],34.364', payload_len: 78 }, { id: 57081, time: '2024-01-19 12:29:20.200', type: 'Tx', address: '93.23.19.159', port: '5000', payload: '#DEMO:ADC_CH1_VOLTAGE [V],0.003,TEMPERATURE [degC],21.769,HUMIDITY [RH],34.364', payload_len: 78 }, { id: 57080, time: '2024-01-19 12:29:14.210', type: 'Tx', address: '93.22.36.40', port: '5000', payload: '2297,473371270,48.13319,-01.63683,110.8,35.1,2.0,5', payload_len: 50 }, { id: 57079, time: '2024-01-19 12:29:14.208', type: 'Rx', address: '93.22.36.40', port: '5000', payload: '2297,473371270,48.13319,-01.63683,110.8,35.1,2.0,5', payload_len: 50 }, { id: 57078, time: '2024-01-19 12:29:10.299', type: 'Rx', address: '93.23.19.159', port: '5000', payload: '#DEMO:ADC_CH1_VOLTAGE [V],0.006,TEMPERATURE [degC],21.787,HUMIDITY [RH],34.341', payload_len: 78 }]
          for (let i = 0; i < resp.length; i++) {
            const pl = resp[i].payload
            const idx = pl.indexOf('#DEMO:')
            // no pattern had
            if (idx === -1) continue
            // Bad request length
            if (idx + '#DEMO:'.length >= pl.length) continue
            const dat = pl.slice(idx + '#DEMO:'.length, pl.length)
            const dats = dat.split(',')
            // Bad pair of data
            if (dats.length === 0 || dats.length % 2 !== 0) continue
            const sensorValues = []
            const numSensorValue = dats.length / 2
            for (let cnt = 0; cnt < numSensorValue; cnt++) {
              const sensorValue = {}
              sensorValue.name = dats[2 * cnt]
              sensorValue.value = dats[2 * cnt + 1]
              if (dats[2 * cnt] === 'ADC_CH1_VOLTAGE') sensorValue.imgFilter = 1
              else if (dats[2 * cnt] === 'TEMPERATURE') sensorValue.imgFilter = 2
              else if (dats[2 * cnt] === 'HUMIDITY') sensorValue.imgFilter = 3
              else sensorValue.imgFilter = 0
              if (cnt < this.numSensorValue) {
                if (sensorValue.name === this.sensorValues[cnt].name && sensorValue.value === this.sensorValues[cnt].value) {
                  sensorValue.changed = 0
                } else {
                  sensorValue.changed = 1
                }
              } else {
                sensorValue.changed = 1
              }
              sensorValues.push(sensorValue)
            }
            this.sensorValues = sensorValues
            this.numSensorValue = numSensorValue
            break
          }
        } else if (response.status === 401) {
          await this.$root.refreshSession()
          if (this.$root.session === 1) {
            const response = await fetch('https://' + window.location.hostname + '/api/udpecho/count', {
              // mode: 'cors',
              credentials: 'include'
            })
            const resp = await response.json()
            // const resp = [{ id: 57084, time: '2024-01-19 12:29:30.108', type: 'Tx', address: '93.23.19.159', port: '5000', payload: '#DEMO:ADC_CH1_VOLTAGE,0.006,TEMPERATURE,21.787,HUMIDITY,34.313', payload_len: 78 }, { id: 57083, time: '2024-01-19 12:29:30.100', type: 'Rx', address: '93.23.19.159', port: '5000', payload: '#DEMO:ADC_CH1_VOLTAGE [V],0.006,TEMPERATURE [degC],21.787,HUMIDITY [RH],34.313', payload_len: 78 }, { id: 57082, time: '2024-01-19 12:29:20.198', type: 'Rx', address: '93.23.19.159', port: '5000', payload: '#DEMO:ADC_CH1_VOLTAGE [V],0.003,TEMPERATURE [degC],21.769,HUMIDITY [RH],34.364', payload_len: 78 }, { id: 57081, time: '2024-01-19 12:29:20.200', type: 'Tx', address: '93.23.19.159', port: '5000', payload: '#DEMO:ADC_CH1_VOLTAGE [V],0.003,TEMPERATURE [degC],21.769,HUMIDITY [RH],34.364', payload_len: 78 }, { id: 57080, time: '2024-01-19 12:29:14.210', type: 'Tx', address: '93.22.36.40', port: '5000', payload: '2297,473371270,48.13319,-01.63683,110.8,35.1,2.0,5', payload_len: 50 }, { id: 57079, time: '2024-01-19 12:29:14.208', type: 'Rx', address: '93.22.36.40', port: '5000', payload: '2297,473371270,48.13319,-01.63683,110.8,35.1,2.0,5', payload_len: 50 }, { id: 57078, time: '2024-01-19 12:29:10.299', type: 'Rx', address: '93.23.19.159', port: '5000', payload: '#DEMO:ADC_CH1_VOLTAGE [V],0.006,TEMPERATURE [degC],21.787,HUMIDITY [RH],34.341', payload_len: 78 }]
            for (let i = 0; i < resp.length; i++) {
              const pl = resp[i].payload
              const idx = pl.indexOf('#DEMO:')
              // no pattern had
              if (idx === -1) continue
              // Bad request length
              if (idx + '#DEMO:'.length >= pl.length) continue
              const dat = pl.slice(idx + '#DEMO:'.length, pl.length)
              const dats = dat.split(',')
              // Bad pair of data
              if (dats.length === 0 || dats.length % 2 !== 0) continue
              const sensorValues = []
              const numSensorValue = dats.length / 2
              for (let cnt = 0; cnt < numSensorValue; cnt++) {
                const sensorValue = {}
                sensorValue.name = dats[2 * cnt]
                sensorValue.value = dats[2 * cnt + 1]
                if (dats[2 * cnt] === 'ADC_CH1_VOLTAGE') sensorValue.imgFilter = 1
                else if (dats[2 * cnt] === 'TEMPERATURE') sensorValue.imgFilter = 2
                else if (dats[2 * cnt] === 'HUMIDITY') sensorValue.imgFilter = 3
                else sensorValue.imgFilter = 0
                if (cnt < this.numSensorValue) {
                  if (sensorValue.name === this.sensorValues[cnt].name && sensorValue.value === this.sensorValues[cnt].value) {
                    sensorValue.changed = 0
                  } else {
                    sensorValue.changed = 1
                  }
                } else {
                  sensorValue.changed = 1
                }
                sensorValues.push(sensorValue)
              }
              this.sensorValues = sensorValues
              this.numSensorValue = numSensorValue
              break
            }
          }
        } else {
          this.$root.session = 0
        }
      } catch (error) {
        // console.error('Fail to get records')
      }
    },
    async setNavItem (num) {
      this.navItem = num
    }
  },
  watch: {
    navItem: function (val, oldVal) {
      if (val === 1) {
        this.updateData()
        if (!this.interval) {
          clearInterval(this.interval)
        }
        this.interval = setInterval(() => {
          this.updateData()
        }, this.refreshRate * 1000)
      } else {
        clearInterval(this.interval)
        this.interval = null
      }
    }
  }
}
</script>

<style>
h1 {
  font-family: Arial, sans-serif;
  font-size: 24px;
  margin-top: 20px;
  margin-bottom: 30px;
  text-align: center;
  max-width: 80%;
  margin-left: auto;
  margin-right: auto;
}

.NavMenu {
  align-items: center;
  margin: 7px 10px;
  margin-bottom: 30px;
}
.NavMenu .NavItem {
  align-items: center;
  background-color: #f8f8ff;
  border-radius: 5px;
  box-shadow: 0 2px 2px rgba(0, 0, 0, 0.2);
  padding: 10px 8px;
  font-weight: bold;
  font-size: 15px;
  font-family: Arial, sans-serif;
}

.data-div {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.value-container {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
  background-color: #f8f8ff;
  border-radius: 10px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  padding: 20px;
}

.value-container img {
  width: 80px;
  height: 80px;
  margin-right: 20px;
}
.value-container span:first-child {
  margin-right: 20px;
}
.value-container span {
  font-size: 20px;
  font-weight: bold;
  text-align: center;
}

.ST87M01_Desc {
  font-family: Arial, sans-serif;
  font-size: 16px;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: auto;
}
.ST87M01_Desc .ST87M01_Desc_list{
  width: fit-content;
  text-align: left;
}
/* .Wrapper {
 background-image: url("../assets/udpdemo_bg.png")
} */
</style>
