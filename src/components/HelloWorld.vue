<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12">
        <h2>Current Camera</h2>
        <code v-if="device">{{ device.label }}</code>
        <div class="border">
            <vue-web-cam
                ref="webcam"
                :device-id="deviceId"
                width="100%"
                @started="onStarted"
                @stopped="onStopped"
                @error="onError"
                @cameras="onCameras"
                @camera-change="onCameraChange"
            />
        </div>
      </v-col>
      <v-col cols="12">
        <select v-model="camera">
            <option>-- Select Device --</option>
            <option
                v-for="device in devices"
                :key="device.deviceId"
                :value="device.deviceId"
            >{{ device.label }}</option>
        </select>
      </v-col>
      <v-col cols="12">
          <v-btn
              icon
              color="blue"
              x-large
              outlined
              @click="onCapture"
            >
              <v-icon>mdi-camera</v-icon>
            </v-btn>
      </v-col>
      <v-col cols="12">
          <v-btn dark color="red" @click="onStop">Stop Camera</v-btn>
          <v-btn dark color="green" @click="onStart">Start Camera</v-btn>
      </v-col>
      <v-col cols="6">
        <h2>Captured Image</h2>
        <figure class="figure">
            <img :src="img" class="img-responsive" />
        </figure>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import { WebCam } from "vue-web-cam";

export default {
    name: "App",
    components: {
        "vue-web-cam": WebCam
    },
    data() {
        return {
            img: null,
            camera: null,
            deviceId: null,
            devices: []
        };
    },
    computed: {
        device: function() {
            return this.devices.find(n => n.deviceId === this.deviceId);
        }
    },
    watch: {
        camera: function(id) {
            this.deviceId = id;
        },
        devices: function() {
            // Once we have a list select the first one
            const first = this.devices[0];
            if (first) {
                this.camera = first.deviceId;
                this.deviceId = first.deviceId;
            }
        }
    },
    methods: {
        onCapture() {
            this.img = this.$refs.webcam.capture();
            console.log("capturing image");
            console.log(this.img);
        },
        onStarted(stream) {
            console.log("On Started Event", stream);
        },
        onStopped(stream) {
            console.log("On Stopped Event", stream);
        },
        onStop() {
            this.$refs.webcam.stop();
        },
        onStart() {
            this.$refs.webcam.start();
        },
        onError(error) {
            console.log("On Error Event", error);
        },
        onCameras(cameras) {
            this.devices = cameras;
            console.log("On Cameras Event", cameras);
        },
        onCameraChange(deviceId) {
            this.deviceId = deviceId;
            this.camera = deviceId;
            console.log("On Camera Change Event", deviceId);
        }
    }
};
</script>