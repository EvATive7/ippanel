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
            variant="text" @click="this.pressed(value)">
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
          type: 6,
          location: 'Loading',
          address: 'Loading',
          status: 0, // 0 Loading; 1 Failed; 2 Success;
          icon: 'mdi-numeric-6-circle'
        },
        ipv4: {
          type: 4,
          location: 'Loading',
          address: 'Loading',
          status: 0,
          icon: 'mdi-numeric-4-circle'
        }
      }
    };
  },
  mounted() {
    this.updateIpv4()
    this.updateIpv6()
  },
  methods: {
    failed(info, error) {
      info.status = 1;
      info.location = 'Failed';
      info.address = `Press to retry. (${error})`;
    },
    updateIpv4() {
      //v4
      const v4info = this.current.ipv4
      if (this.debug) {
        v4info.location = '中国 北京市 北京市'
        v4info.address = '39.156.66.10'
        v4info.status = 2
      } else {
        v4info.location = 'Loading'
        v4info.address = 'Reuqesting to update.cz88.net'
        v4info.status = 0
        axios.get(`https://update.cz88.net/api/cz88/ip/base?ip=`).then(response => {
          v4info.location = response.data.data['actionAddress'][0]
          v4info.address = response.data.data.ip
          v4info.status = 2
        }).catch(error => {
          this.failed(v4info, error)
        });
      }
    },
    updateIpv6() {
      //v6
      const v6info = this.current.ipv6
      if (this.debug) {
        v6info.location = '中国 北京市 北京市'
        v6info.address = '2400:da00:2::29'
        v6info.status = 2
      } else {
        v6info.location = 'Loading'
        v6info.address = 'Reuqesting to v6.ip.zxinc.org'
        v6info.status = 0
        axios.get('https://v6.ip.zxinc.org/info.php?type=json').then(response => {
          v6info.location = response.data.data.location
          v6info.address = response.data.data.myip
          v6info.status = 2
        }).catch(error => {
          this.failed(v6info, error)
        });
      }
    },

    pressed(info) {
      switch (info.status) {
        case 2:
          this.copyToClipboard(info.location + "\n" + info.address)
          break
        case 1:
          switch (info.type) {
            case 6:
              this.updateIpv6()
              break
            case 4:
              this.updateIpv4()
              break
          }
          break
      }
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