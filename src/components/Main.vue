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
      debug: false,
      current: {
        ipv6: {
          location: 'Loading',
          address: 'Loading',
          icon: 'mdi-numeric-6-circle'
        },
        ipv4: {
          location: 'Loading',
          address: 'Loading',
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
      const failed = (info) => {
        info.location = 'Failed';
        info.address = 'Failed';
      }

      const axiosConfig = {
        headers: {
          //'Origin': 'http://ip.zxinc.org',
          //'Referer': 'http://ip.zxinc.org/'
        }
      }

      //v4
      const v4info = this.current.ipv4
      if (this.debug) {
        v4info.location = '中国 北京市 北京市'
        v4info.address = '39.156.66.10'
      } else {
        axios.get(`https://update.cz88.net/api/cz88/ip/base?ip=`, axiosConfig).then(response => {
          v4info.location = response.data.data['actionAddress'][0]
          v4info.address = response.data.data.ip
        }).catch(error => {
          failed(v4info)
        });
      }

      //v6
      const v6info = this.current.ipv6
      if (this.debug) {
        v6info.location = '中国 北京市 北京市'
        v6info.address = '2400:da00:2::29'
      } else {
        axios.get('https://v6.ip.zxinc.org/info.php?type=json', axiosConfig).then(response => {
          v6info.location = response.data.data.location
          v6info.address = response.data.data.myip
        }).catch(error => {
          failed(v6info)
        });
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

<style>
* {
  user-select: none;
}
</style>