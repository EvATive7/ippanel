<template>
  <v-container class="fill-height">
    <v-responsive class="align-center fill-height mx-auto">

      <div class="text-center">
        <v-icon align="center" justify-center class="mb-4 mx" color="info" icon="mdi-map-marker" size="150" />
        <div class="text-body-2 font-weight-light mb-n1">This is your</div>

        <h1 class="text-h2 font-weight-bold">IP Location</h1>
      </div>

      <div class="py-4" />

      <v-row>
        <v-col cols="12" lg="6" v-for="(value, i) in [this.current.ipv6, this.current.ipv4]" :key="i">
          <v-card class="py-4" color="surface-variant" :prepend-icon="value.icon" rounded="xl" rel="noopener noreferrer"
            variant="text" @click="this.copy(value)">
            <template #title>
              <h2 class="text-h5 font-weight-bold">
                <Marquee>
                  {{ value.location }}
                </Marquee>
              </h2>
            </template>
            <template #subtitle>
              <div class="text-subtitle-1">
                {{ value.address }}
              </div>
            </template>
            <v-overlay opacity=".06" scrim="primary" contained model-value persistent />
          </v-card>
        </v-col>
      </v-row>
      <div class="py-8" />
    </v-responsive>
  </v-container>
</template>

<script>
import Marquee from './Marquee.vue'
import axios from 'axios'
export default {
  data() {
    return {
      debug: true,
      current: {
        ipv6: {
          location: '正在加载',
          address: '正在加载',
          icon: 'mdi-numeric-6-circle'
        },
        ipv4: {
          location: '正在加载',
          address: '正在加载',
          icon: 'mdi-numeric-4-circle'
        }
      }
    };
  },
  mounted() {
    this.updateIp()
  },
  methods: {
    updateIp() {
      var update = [
        {
          type: this.current.ipv4,
          url: 'https://v4.ip.zxinc.org/info.php?type=json'
        },
        {
          type: this.current.ipv6,
          url: 'https://v6.ip.zxinc.org/info.php?type=json'
        }
      ]
      for (const u of update) {
        if (this.debug) {
          console.log(u.type.location);
          u.type.location = 'testTesttestTesttestTesttestTesttestTesttestTesttestTesttestTesttestTesttestTest'
          u.type.address = 'testTesttestTesttestTesttestTesttestTesttestTesttestTesttestTesttestTesttestTest'
        } else {
          axios.get(u.url).then(response => {
            u.type.location = response.data.data.location
            u.type.address = response.data.data.myip
          }).catch(error => {
            u.type.location = '获取失败';
            u.type.address = '获取失败';
          });
        }

      }
    },
    copy(info) {
      this.copyToClipboard(info.location + "\n" + info.address)
    },
    copyToClipboard(text) {
      var textarea = document.createElement("textarea");
      textarea.value = text;
      document.body.appendChild(textarea);
      textarea.select();
      document.execCommand("copy");
      document.body.removeChild(textarea);
    }
  }

};
</script>
