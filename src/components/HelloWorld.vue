<template>
  <v-container fill-height fluid>
    <v-layout align-center justify-center row fill-height class="mx-5">
      <v-flex xs12 md6 lg6 xl5>
        <div class="text-xs-left hidden-md-and-up">
          <h1 style="font-size:6rem">
            <span>您的</span>
            <br />
            <span class="font-weight-black">Dota2</span>
            <br />
            <span>私人管家</span>
          </h1>
        </div>
        <div class="text-xs-left">
          <h1 class="display-4 hidden-sm-and-down">
            <span>您的</span>
            <br />
            <span class="font-weight-black">Dota2</span>
            <br />
            <span>私人管家</span>
          </h1>
          <h2 class="display-1 my-4 hidden-sm-and-down">
            <span>我们提供实时分析的 Dota2 数据及游戏工具.</span>
          </h2>
          <div v-if="updateList2">
            <v-btn
              :href="updateList2.installer_url"
              rounded
              large
              dark
              color="deep-purple accent-4"
              @click="download.dialog = true, download.snackbar = true, download.tip = true "
            >
              下载游戏助手
              V{{ updateList2.version }}
            </v-btn>
            <v-btn rounded large outlined @click="download.dialog = true" class="ml-2">更新日志</v-btn>
            <v-snackbar v-model="download.snackbar" :bottom="true" :left="true">正在下载 Dota2 助手</v-snackbar>
            <v-dialog v-model="download.dialog" width="800">
              <v-card>
                <div class="text-xs-center pt-5">
                  <h4 v-if="download.tip" class="display-1">下载已自动开始</h4>
                  <v-btn
                    :href="updateList2.installer_url"
                    rounded
                    @click="download.tip = true, download.snackbar = true"
                  >安装版下载</v-btn>
                  <v-btn
                    :href="updateList2.green_url"
                    rounded
                    @click="download.tip = true, download.snackbar = true"
                  >绿色版下载</v-btn>
                </div>
                <v-timeline align-top dense>
                  <v-timeline-item v-for="(item, i) in updateLog.ver" :key="i" color="accent" small>
                    <v-layout pt-3>
                      <v-flex xs3>
                        <strong>V{{ item['@id'] }}</strong>
                        <div class="caption">{{ item['@date'] }}</div>
                      </v-flex>
                      <v-flex v-if="Array.isArray(item.log)">
                        <span v-for="(log, k) in item.log" :key="k">
                          {{ log }}
                          <br />
                        </span>
                      </v-flex>
                      <v-flex v-else>
                        <span>{{ item.log }}</span>
                      </v-flex>
                    </v-layout>
                  </v-timeline-item>
                </v-timeline>
              </v-card>
            </v-dialog>
          </div>
          <div v-else>
            <v-progress-linear indeterminate color="accent" />
          </div>
        </div>
        <div>
          <v-icon x-large>fas fa-angle-double-down faa-bounce animated</v-icon>
        </div>
      </v-flex>

      <v-flex xs12 md6 lg5 xl4 class="hidden-sm-and-down">
        <SVGComponent />
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import SVGComponent from './Svg.vue';

export default {
  components: {
    SVGComponent,
  },
  data: () => ({
    logs: null,
    updateList2: null,
    updateLog: null,
    download: {
      dialog: false,
      snackbar: false,
      tip: false,
    },
  }),
  async mounted() {
    const self = this;
    fetch('https://api.steamhub.cn/api/dota2/tool/log', {
      headers: new Headers({
        'Content-Type': 'application/json',
      }),
    })
      .then(response => response.json())
      .then((data) => {
        /* eslint prefer-destructuring: ["error", {AssignmentExpression: {array: false}}] */
        self.logs = data[0];
        self.updateList2 = self.logs.updateList2.root;
        self.updateLog = self.logs.updateLog.root;
      })
      .catch((err) => {
        console.log('Fetch Error :-S', err);
      });
  },
};
</script>
