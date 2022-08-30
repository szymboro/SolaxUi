<template>
  <div class="">
    <nav class="w-full bg-amber-500 p-5">
      <h1 class="text-3xl text-white font-bold inline m-3">Solax Cloud</h1>
      <button
        class="text-white m-5 p-1 rounded-md absolute top-1 right-0"
        onclick="window.open('https://www.solaxcloud.com/settingNew/index.html#/login','_blank')"
      >
        <iconify-icon class="text-xl" icon="fa6-solid:gear"></iconify-icon>
      </button>
    </nav>
    <body class="m-5 grid grid-cols-1 md:grid-cols-3 gap-4">
      <div
        class="bg-white min-w-fit max-h-60 drop-shadow-md rounded-md p-5 m-1 flex-initial grid place-items-center"
      >
        <h1 class="text-2xl font-bold">Yield Total</h1>
        <p class="text-5xl">{{ solaxCloud.yieldtotal }}KWh</p>
      </div>
      <div
        class="bg-white min-w-fit max-h-60 drop-shadow-md rounded-md p-5 m-1 flex-initial"
      >
        <h1 class="text-2xl font-bold">Inventer Info</h1>
        <div class="">Api Response: {{ resp.success }}</div>
        <div class="">Api Exception: {{ resp.exception }}</div>
        <p class="">Inverter SN: {{ solaxCloud.inverterSN }}</p>
        <p class="">Dongle SN: {{ solaxCloud.sn }}</p>
        <template v-if="getType">
          <p class="">Inventer Type: {{ getType.name }}</p>
          <p class="">Inventer Status: {{ getStatus.name }}</p>
        </template>
        <p class="">Upload Time: {{ solaxCloud.uploadTime }}</p>
      </div>

      <div
        class="bg-white min-w-fit drop-shadow-md rounded-md p-5 m-1 flex-initial"
      >
        <h1 class="text-2xl font-bold">Today Info</h1>
        <p class="">Inverter SN: {{ solaxCloud.yieldtoday }}KWh</p>
        <p class="">AC Power: {{ solaxCloud.acpower }}W</p>
        <p class="">Grid Power: {{ solaxCloud.feedinpower }}W</p>
        <p class="">Grid Energy: {{ solaxCloud.feedinenergy }}KWh</p>
        <p class="">Consume Energy: {{ solaxCloud.consumeenergy }}KWh</p>
        <p class="" v-show="solaxCloud.feedinpowerM2 != 0">
          Grid Power Meeter 2: {{ solaxCloud.feedinpowerM2 }}KWh
        </p>
        <p class="">Soc: {{ solaxCloud.soc }}%</p>
        <p class="">EPS Power R: {{ solaxCloud.peps1 }}W</p>
        <p class="">EPS Power S: {{ solaxCloud.peps2 }}W</p>
        <p class="">EPS Power T: {{ solaxCloud.peps3 }}W</p>
        <p class="" v-show="solaxCloud.powerdc1 != null">
          DC Power 1: {{ solaxCloud.powerdc1 }}W
        </p>
        <p class="" v-show="solaxCloud.powerdc2 != null">
          DC Power 2: {{ solaxCloud.powerdc2 }}W
        </p>
        <p class="" v-show="solaxCloud.powerdc3 != null">
          DC Power 3: {{ solaxCloud.powerdc3 }}W
        </p>
        <p class="" v-show="solaxCloud.powerdc4 != null">
          DC Power 4: {{ solaxCloud.powerdc4 }}W
        </p>
        <p class="" v-show="solaxCloud.powerdc3 != null">
          Battery Power Total: {{ solaxCloud.batPower }}W
        </p>
        <p class="">Battery Status: {{ solaxCloud.batStatus }}</p>
      </div>
    </body>
    <!-- <div>
      <div class="bg-white min-w-fit drop-shadow-md rounded-md p-5 m-6">
        <h1 class="text-2xl font-bold">Raw Data</h1>
        <pre class="overflow-auto">{{ resp }}</pre>
      </div>
    </div> -->
  </div>
</template>

<script>
import data from "./data.json";

export default {
  name: "App",
  data() {
    return {
      solaxCloud: [],
      resp: [],
      credentials: data.creds,
      types: data.inverterType,
      statuses: data.inverterStatus,
      timer: "",
    };
  },
  computed: {
    getType() {
      const number = this.solaxCloud.inverterType;
      const type = this.types.find((item) => item.id === number);
      return type;
    },
    getStatus() {
      const number = this.solaxCloud.inverterStatus;
      const status = this.statuses.find((item) => item.id === number);
      return status;
    },
  },
  methods: {
    getList() {
      this.axios({
        method: "get",
        url: "https://www.solaxcloud.com/proxyApp/proxy/api/getRealtimeInfo.do",
        params: {
          tokenId: this.credentials.token,
          sn: this.credentials.sn,
        },
      })
        .then((response) => (this.resp = response.data))
        .then((response) => (this.solaxCloud = this.resp.result));
    },
  },
  created() {
    this.timer = setInterval(this.getList, 120000);
  },
  mounted() {
    this.getList();
  },
};
</script>

<style scoped>
#sensitive {
  content: var(--hidden);
  filter: blur(4px);
  display: inline;
}
</style>
