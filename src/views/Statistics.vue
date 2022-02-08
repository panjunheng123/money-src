<template>
  <Layout>
    <Tabs :data-source="recordTypeList" :value.sync="type"></Tabs>
    <Tabs class-prefix="date" :data-source="dateTypeList" :value.sync="date"></Tabs>
    <ol v-if="groupedDayList.length>0&&date==='day'">
      <li v-for="(group,index) in groupedDayList" :key="index">
        <h3 class="title">{{ dayBeautify(group.title) }} <span>￥{{ group.total }}</span></h3>
        <ol>
          <li v-for="item in group.items" :key="item.id" class="record">
            <Icon :name="tagIconNameString(item.tags)"></Icon>
            <span>{{ tagNameString(item.tags) }}</span>
            <span class="notes" :style="{marginRight:'auto'}">{{ item.notes }}</span>
            <span>￥{{ item.amount }}</span>
          </li>
        </ol>
      </li>
    </ol>
    <ol v-else-if="groupedMonthList.length>0&&date==='month'">
      <li v-for="(group,index) in groupedMonthList" :key="index">
        <h3 class="title">{{ monthBeautify(group.title) }} <span>￥{{ group.total }}</span></h3>
        <ol>
          <li v-for="item in group.items" :key="item.id" class="record">
            <Icon :name="tagIconNameString(item.tags)"></Icon>
            <span>{{ tagNameString(item.tags) }}</span>
            <span class="notes" :style="{marginRight:'auto'}">{{ item.notes }}</span>
            <span>￥{{ item.amount }}</span>
          </li>
        </ol>
      </li>
    </ol>
    <div v-else class="noResult">
      目前没有相关记录
    </div>
  </Layout>
</template>

<style scoped lang="scss">
.noResult {
  padding: 16px;
  text-align: center;
}

%item {
  padding: 8px 16px;
  line-height: 24px;
  display: flex;
  justify-content: space-between;
  align-content: center;
}

.title {
  @extend %item
}

.item {
  @extend %item;
}

.record {
  background: rgb(236, 236, 236);
  @extend %item;

  > svg {
    width: 24px;
    height: 24px;
    margin-right: 12px;
  }
}

.notes {
  margin-right: auto;
  margin-left: 16px;
  color: #999;
}

</style>

<script lang="ts">
import Vue from 'vue';
import {Component} from 'vue-property-decorator';
import Tabs from '@/components/Tabs.vue';
import recordTypeList from '@/constants/recordTypeList';
import dayjs from 'dayjs';
import clone from '@/lib/clone';

@Component({
  components: {Tabs},
})
export default class Statistics extends Vue {
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

  dayBeautify(string: string) {
    const day = dayjs(string);
    const now = dayjs();
    if (day.isSame(now, 'day')) {
      return '今天';
    } else if (day.isSame(now.subtract(1, 'day'), 'day')) {
      return '昨天';
    } else if (day.isSame((now), 'year')) {
      return day.format('M月D日');
    } else {
      return day.format('YYYY年M月D日');
    }
  }

  monthBeautify(string: string) {
    const day = dayjs(string);
    const now = dayjs();
    if (day.isSame(now, 'month')) {
      return '本月';
    } else if (day.isSame((now), 'year')) {
      return day.format('M月');
    } else {
      return day.format('YYYY年M月');
    }
  }

  get recordList() {
    return this.$store.state.recordList;
  }

  get groupedDayList() {
    const {recordList} = this;

    const newList = clone(recordList).filter((r: RecordItem) => r.type === this.type).sort((a: RecordItem, b: RecordItem) => dayjs(b.createdAt).valueOf() - dayjs(a.createdAt).valueOf());
    if (newList.length === 0) {return [] as Result;}
    type Result = { title: string, total?: number, items: RecordItem[] }[]
    const result: Result = [{title: dayjs(newList[0].createdAt).format('YYYY-MM-DD'), items: [newList[0]]}];
    for (let i = 1; i < newList.length; i++) {
      const current = newList[i];
      const last = result[result.length - 1];
      if (dayjs(last.title).isSame(dayjs(current.createdAt), 'day')) {
        last.items.push(current);
      } else {
        result.push({title: dayjs(current.createdAt).format('YYYY-MM-DD'), items: [current]});
      }
    }
    result.map(group => {
      group.total = group.items.reduce((sum, item) => sum + item.amount, 0);
    });
    return result;
  }

  get groupedMonthList() {
    const {recordList} = this;

    const newList = clone(recordList).filter((r: RecordItem) => r.type === this.type).sort((a: RecordItem, b: RecordItem) => dayjs(b.createdAt).valueOf() - dayjs(a.createdAt).valueOf());
    if (newList.length === 0) {return [] as Result;}
    type Result = { title: string, total?: number, items: RecordItem[] }[]
    const result: Result = [{title: dayjs(newList[0].createdAt).format('YYYY-MM-DD'), items: [newList[0]]}];
    for (let i = 1; i < newList.length; i++) {
      const current = newList[i];
      const last = result[result.length - 1];
      if (dayjs(last.title).isSame(dayjs(current.createdAt), 'month')) {
        last.items.push(current);
      } else {
        result.push({title: dayjs(current.createdAt).format('YYYY-MM-DD'), items: [current]});
      }
    }
    result.map(group => {
      group.total = group.items.reduce((sum, item) => sum + item.amount, 0);
    });
    return result;
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
  padding: 0 !important;

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