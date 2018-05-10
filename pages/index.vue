<template>
  <div>
    <button @click="getDevices">Lol</button>
  </div>
</template>

<script>
  export default {
    methods: {
      getDevices () {
        console.log('lol')
        navigator.bluetooth.requestDevice({"acceptAllDevices":true})
        .then(device => {
          log('> Name:             ' + device.name);
          log('> Id:               ' + device.id);
          log('> Connected:        ' + device.gatt.connected);
        })
      }
    },
    mounted () {

    }
  }
</script>

<style>
</style>
