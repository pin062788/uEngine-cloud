<template xmlns:v-on="http://www.w3.org/1999/xhtml">
  <div v-if="categoryItem" style="padding: 20px">
    <span class="md-title">{{categoryItem.header}}</span>
    <br>
    <br>
    <div class="header-top-line"></div>
    <br>
    <br>

    <md-layout v-if="dcosData.devopsApps" :md-gutter="16">
      <md-layout md-flex="30">
        <div>
          <span class="md-subheading">{{categoryItem.title}}</span>
          <br><br><br>
          <span class="md-caption">{{categoryItem.description}}</span>
          <br><br><br>
          <a>문서 보기</a>
          <br><br><br>
          <table style="width: 100%">
            <tr>
              <td>버전</td>
              <td>{{categoryItem.version}}</td>
            </tr>
            <tr>
              <td>타입</td>
              <td>{{categoryItem.type}}</td>
            </tr>
          </table>
        </div>
      </md-layout>
      <md-layout md-flex="70">
        <div class="bold">앱 이름:</div>
        <md-input-container>
          <label>고유한 이름 입력</label>
          <md-input v-model="appName"></md-input>
        </md-input-container>
        <br><br>
        <md-layout md-flex="100" :md-gutter="16">
          <md-layout>
            <div class="bold">외부 접속 주소:</div>
            <md-input-container>
              <label>외부 프로덕션 도메인 주소</label>
              <md-input v-model="externalProdDomain"></md-input>
            </md-input-container>
            <md-input-container>
              <label>외부 스테이징 도메인 주소</label>
              <md-input readonly v-model="externalStgDomain"></md-input>
            </md-input-container>
            <md-input-container>
              <label>외부 개발 도메인 주소</label>
              <md-input readonly v-model="externalDevDomain"></md-input>
            </md-input-container>
          </md-layout>
          <md-layout>
            <div class="bold">내부 접속 주소:</div>
            <md-input-container>
              <label>내부 프로덕션 주소</label>
              <md-input readonly v-model="internalProdDomain"></md-input>
            </md-input-container>
            <md-input-container>
              <label>내부 스테이징 주소</label>
              <md-input readonly v-model="internalStgDomain"></md-input>
            </md-input-container>
            <md-input-container>
              <label>내부 개발 주소</label>
              <md-input readonly v-model="internalDevDomain"></md-input>
            </md-input-container>
          </md-layout>
        </md-layout>
        <md-layout md-flex="50">
          <div class="bold">리소스:</div>
          <md-layout md-flex="100" :md-gutter="16">
            <md-layout>
              <md-input-container>
                <label>cpu</label>
                <md-input type="number" v-model.number="cpu"></md-input>
              </md-input-container>
            </md-layout>
            <md-layout>
              <md-input-container>
                <label>메모리 (MB)</label>
                <md-input type="number" v-model.number="mem"></md-input>
              </md-input-container>
            </md-layout>
            <md-layout>
              <md-input-container>
                <label>인스턴스 수</label>
                <md-input type="number" v-model.number="instances"></md-input>
              </md-input-container>
            </md-layout>
          </md-layout>
        </md-layout>
        <md-layout md-flex="50" :md-gutter="16">
          <div class="bold">깃랩 그룹: (생성 후에도 변경가능합니다.)</div>
          <md-layout md-flex="100">
            <md-input-container>
              <md-select v-model="namespace">
                <md-option value="">내 아이디로 생성</md-option>
                <md-option v-for="group in groups" :value="group.path">{{group.name}}</md-option>
              </md-select>
            </md-input-container>
          </md-layout>
        </md-layout>


        <md-button class="md-primary md-raised" @click="create">생성하기</md-button>
      </md-layout>
    </md-layout>
  </div>
</template>
<script>
  import DcosDataProvider from '../DcosDataProvider'
  import PathProvider from '../PathProvider'
  export default {
    mixins: [DcosDataProvider, PathProvider],
    props: {},
    data() {
      return {
        defaultHost: window.config['default-host'],
        categoryItem: null,
        cpu: 0.4,
        mem: 512,
        instances: 1,
        //appNumber: 1,
        namespace: "",
        groups: [],
        projectId: "",
        projects: [],
        existRepository: false,
        appName: null,
        externalProdDomain: null,
        externalStgDomain: null,
        externalDevDomain: null,
        internalProdDomain: null,
        internalStgDomain: null,
        internalDevDomain: null,
        prodPort: null,
        stgPort: null,
        devPort: null
      }
    },
    mounted(){
      var me = this;
      me.getGroupsIncludMe(localStorage['gitlab-id'], function (groups) {
        if (groups) {
          me.groups = groups;
        }
      });
      this.getCategoryItem(me.categoryItemId, function (item) {
        me.categoryItem = item;
      });
    },
    watch: {
      'dcosData.devopsApps': {
        handler: function (newVal, oldVal) {
          if (!newVal) {
            return;
          }
          var min = 1;
          var max = 33;
          this.appNumber = 1;
          for (var i = min; i <= max; i++) {
            var canUse = true;
            for (var appId in newVal) {
              if (i == newVal[appId].number) {
                canUse = false;
              }
            }
            if (canUse) {
              this.appNumber = i;
              break;
            }
          }
        },
        deep: true
      },
      appName: function (val) {
        var me = this;
        if (!val) {
          val = '';
        }
        var prodPort = 10010 + ((this.appNumber - 1) * 3) + 1;
        var stgPort = 10010 + ((this.appNumber - 1) * 3) + 2;
        var devPort = 10010 + ((this.appNumber - 1) * 3) + 3;
        this.prodPort = prodPort;
        this.stgPort = stgPort;
        this.devPort = devPort;
        this.internalProdDomain = 'marathon-lb-internal.marathon.mesos:' + prodPort;
        this.internalStgDomain = 'marathon-lb-internal.marathon.mesos:' + stgPort;
        this.internalDevDomain = 'marathon-lb-internal.marathon.mesos:' + devPort;

        this.externalProdDomain = val + '.' + this.defaultHost;
      },
      externalProdDomain: function (val) {
        if (val) {
          let split = val.split('.');
          let subDomain = split[0];
          if (subDomain && subDomain.length > 0) {
            var left = val.substring(subDomain.length, val.length);
            this.externalStgDomain = subDomain + '-stg' + left;
            this.externalDevDomain = subDomain + '-dev' + left;
          }
        } else {
          this.externalStgDomain = '';
          this.externalDevDomain = '';
        }
      }
    }
    ,
    methods: {
      create: function () {
        var me = this;
        var appCreate = {
          categoryItemId: me.categoryItemId,
          cpu: me.cpu,
          mem: me.mem,
          instances: me.instances,
          appNumber: me.appNumber,
          projectId: me.projectId,
          appName: me.appName,
          externalProdDomain: me.externalProdDomain,
          externalStgDomain: me.externalStgDomain,
          externalDevDomain: me.externalDevDomain,
          namespace: me.namespace
        };
        me.createApp(appCreate, function (response) {
          if (response) {
            me.$router.push(
              {
                name: 'appsDetail',
                params: {appName: me.appName}
              }
            );
          }
        });
      }
    }
  }
</script>

<style scoped lang="scss" rel="stylesheet/scss">

</style>
