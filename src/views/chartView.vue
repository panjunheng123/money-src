<template>
  <Layout>
    <Tabs :data-source="recordTypeList" :value.sync="type"></Tabs>
    <Tabs class-prefix="date" :data-source="dateTypeList" :value.sync="date"></Tabs>
    <Chart :options="x"></Chart>
  </Layout>
</template>

<script lang="ts">
import Vue from 'vue';
import {Component} from 'vue-property-decorator';
import Tabs from '@/components/Tabs.vue';
import recordTypeList from '@/constants/recordTypeList';
import Chart from '@/components/Chart.vue';

@Component({
  components: {Tabs, Chart},
})
export default class Statistics extends Vue {
  get x() {
    return {
      xAxis: {
        type: 'category',
        data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
      },
      yAxis: {
        type: 'value'
      },
      series: [
        {
          data: [150, 230, 224, 218, 135, 147, 260],
          type: 'line'
        }
      ],
      tooltip: {show: true}
    };
  }

  dateTypeList = [
    {text: '按天', value: 'day'},
    {text: '按月', value: 'month'},
  ];
  date = 'day';

  // eslint-disable-next-line no-undef
  tagNameString(tags: Tag[]) {
    return tags.length === 0 ? '无' : tags.map(t => t.name).join('，');
  }

  // eslint-disable-next-line no-undef
  tagIconNameString(tags: Tag[]) {
    return tags.length === 0 ? '无' : tags.map(t => t.iconName).join('，');
  }

  get recordList() {
    return this.$store.state.recordList;
  }

  Created() {
    this.$store.commit('fetchRecords');
  }

  type = '-';
  recordTypeList = recordTypeList;
}
</script>

<style lang="scss" scoped>
.echarts {
  max-width: 100%;
  height: 400px;
}

::v-deep .date-tabs-item {
  background-color: transparent;
  height: 40px;
  font-size: 20px;

  &.selected {

    background-color: rgb(236, 236, 236);

    &::after {
      display: none;
    }
  }
}

::v-deep .interval-tabs-item {
  height: 48px;
}
</style>