<!--eslint-disable-->
<template>

    <div class="col history-list-parent-col">

      <spinner className="history-spinner hidden" />

      <div class="row history-list-view">

        <select v-if="historyJSON.names.length > 0" v-model="selectedHistory" class="form-control" size="17" border="0" @click="check_deselect" tabindex="6">
          <option style="margin: 1px" v-for="option in historyJSON.names"
                  v-bind:class="{fail: check_status(option)=='fail', pass: check_status(option)=='pass'}"
                  :key="option.value" v-bind:value="{ name_state_type_cd: option.name_state_type_cd, submit_count: option.submit_count, nr_num: option.nr_num, name: option.name, score: option.score}">
            {{ option.name }}
          </option>
        </select>
        <div v-else class="empty-list">( No data )</div>
      </div>

    </div>


</template>

<script>
/* eslint-disable */

  import spinner from '@/components/application/spinner.vue';

  export default {
    name: 'historyList',
    components: {
      spinner,
    },
    data: function() {
      return {
        selectedHistory: ''
      }
    },
    computed: {
      historyJSON() {
        if (this.$store.getters.historiesJSON != null) {
          this.setSelectedHistory();
          return this.$store.getters.historiesJSON;
        } else {
          return {'names': []};
        }
      },
    },
    methods: {
      setHistoryInfo() {
        if (this.selectedHistory != '') {
          this.$store.dispatch('resetHistoriesInfo');
          this.$store.dispatch('getHistoryInfo', this.selectedHistory);
        }
      },
      setSelectedHistory() {
        if (this.$store.getters.currentHistory != null)
          this.selectedHistory = this.$store.getters.currentHistory;
        else
          this.selectedHistory = this.$store.getters.historiesJSON.names[0];
      },
      check_deselect() {
        if (this.$store.getters.currentHistory === this.selectedHistory) {
          this.selectedHistory = '';
          this.$store.dispatch('resetHistoriesInfo');
        }
      },
      check_status(option) {
        if (option.submit_count < 4 && (option.name_state_type_cd!='R')) {
          return 'pass'
        } else {
          return 'fail'
        }
      },
    },
    watch: {
      selectedHistory: {
        handler(selection) {
          if (selection != undefined) {
            if (this.check_status(selection) == 'pass') {
              $("#historyInfo").removeClass();
              $("#historyInfo").addClass("col history-info-view border-pass");
              $("#currentHistoryName").removeClass();
              $("#currentHistoryName").addClass("pass");
            }
            else {
              $("#historyInfo").removeClass();
              $("#historyInfo").addClass("col history-info-view border-fail");
              $("#currentHistoryName").removeClass();
              $("#currentHistoryName").addClass("fail");
            }
            this.$store.commit('currentHistory', selection);
            this.setHistoryInfo();
          } else {
            $("#historyInfo").removeClass();
            $("#historyInfo").addClass("col history-info-view border-fail");
          }
        }
      },
    }
  }
</script>

<style scoped>
</style>
<style>

  /* hide the panel content when spinner is showing, ie: results are loading */
  .spinner:not(.hidden) + .history-list-view {
    display: none;
  }

  .history-list-parent-col {
    min-width: 800px;
  }
  .history-list-view {
    padding: 0 10px;
    height: 100%;
  }

  .history-list-view option {
    padding: 5px;
  }

  .pass {
    background-color: #b6d7a8;
  }
  .concern {
    background-color: #ffe680;
  }
  .fail {
    background-color: #ea9999;
  }
  .border-fail {
    border: 1px solid #ea9999;
  }
  .border-concern {
    border: 1px solid #ffe680;
  }
  .border-pass {
    border: 1px solid #b6d7a8;
  }
  .empty-list {
    padding: 5rem;
    line-height: 1.5;
    border-radius: .2rem;
    font-size: 14px;
    margin-bottom: 2px;
        color: #6c757d;
    background-color: #f2f2f2;
    background-clip: padding-box;
    border: 1px solid #ced4da;
    width: 100%;
    text-align: center;
  }
</style>
