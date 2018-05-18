<template>
  <div>
    <button @click="getDevices">Find devices</button>
    <template v-if="device">
      <h3>Sensor value</h3>
      <div>{{sensorValue}}</div>
      <h3>Send value</h3>
      <div>
        <input type="text" v-model="ssid" />
        <input type="text" v-model="pass" />
        <button @click="sendMessage">Send</button>
        <button @click="getSsid">Get SSID</button>
      </div>
    </template>
  </div>
</template>

<script>
  function ab2str(buf) {
    return String.fromCharCode.apply(null, new Uint8Array(buf));
  }

  function str2ab(str) {
    let buf = new ArrayBuffer(str.length);
    let bufView = new Uint8Array(buf);
    for (let i = 0, strLen = str.length; i < strLen; i++) {
      bufView[i] = str.charCodeAt(i);
    }
    return buf;
  }

  export default {
    data () {
      return {
        sensorValue: '',
        ssid: '',
        pass: '',
        device: null,
        sendCharacteristic: null
      }
    },
    methods: {
      async getDevices () {
        let device = await navigator.bluetooth.requestDevice({
          acceptAllDevices: true,
          // filters: [ {services: ['6e400001-b5a3-f393-e0a9-e50e24dcca9e']} ],
          optionalServices: ['6e400001-b5a3-f393-e0a9-e50e24dcca9e']
        })

        let server = await device.gatt.connect()

        let service = await server.getPrimaryService('6e400001-b5a3-f393-e0a9-e50e24dcca9e')

        let characteristic = await service.getCharacteristic('6e400003-b5a3-f393-e0a9-e50e24dcca9e')

        let char = await characteristic.startNotifications()

        char.addEventListener('characteristicvaluechanged', this.handleChange)

        this.sendCharacteristic = await service.getCharacteristic('6e400002-b5a3-f393-e0a9-e50e24dcca9e')
        this.device = device

      },
      handleChange (event) {
        // console.log(event.target.value)
        this.sensorValue = ab2str(event.target.value.buffer)
      },
      async sendMessage () {
        console.log(this.ssid, this.pass)
        await this.sendCharacteristic.writeValue(str2ab('ssid' + this.ssid));
        await this.sendCharacteristic.writeValue(str2ab('pass' + this.pass));
      },
      getSsid () {
        this.sendCharacteristic.writeValue(str2ab('read'));
      }
    },
    mounted () {

    }
  }
</script>

<style>
</style>
