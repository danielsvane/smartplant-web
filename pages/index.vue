<template>
  <div>
    <button @click="getDevices">Find devices</button>
    <h3>Sensor value</h3>
    <div>{{sensorValue}}</div>
  </div>
</template>

<script>
  function ab2str(buf) {
    return String.fromCharCode.apply(null, new Uint8Array(buf));
  }

  export default {
    data () {
      return {
        sensorValue: ''
      }
    },
    methods: {
      getDevices () {
        navigator.bluetooth.requestDevice({
          "acceptAllDevices":true
        })
        .then(device => device.gatt.connect())
        .then(server => {
          return server.getPrimaryService('6e400001-b5a3-f393-e0a9-e50e24dcca9e')
        })
        .then(service => {
          return service.getCharacteristic('6e400003-b5a3-f393-e0a9-e50e24dcca9e')
        })
        .then(characteristic => {
          return characteristic.startNotifications()
        })
        .then(char => {
            char.addEventListener('characteristicvaluechanged', this.handleChange)
        })
      },
      handleChange (event) {
        // console.log(event.target.value)
        this.sensorValue = ab2str(event.target.value.buffer)
      }
    },
    mounted () {

    }
  }
</script>

<style>
</style>
