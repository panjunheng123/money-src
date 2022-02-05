<template>
  <Layout class-prefix="layout">
    <div>
      <div class="notes">
        <FormItem field-name="备注"
                  placeholder="点击写备注..."
                  :value.sync="record.notes"
        />
      </div>
      <NumberPad @update:value="onUpdateAmount" @submit="saveRecord"/>
    </div>
    <Tags @update:value="record.tags=$event"/>
    <Tabs :data-source="recordTypeList" :value.sync="record.type"></Tabs>
  </Layout>
</template>

<script lang="ts">
import Vue from 'vue';
import NumberPad from '@/components/Money/NumberPad.vue';
import FormItem from '@/components/Money/FormItem.vue';
import Tags from '@/components/Money/Tags.vue';
import {Component} from 'vue-property-decorator';
import recordTypeList from '@/constants/recordTypeList';
import Tabs from '@/components/Tabs.vue';

@Component({
  components: {Tabs, FormItem, Tags, NumberPad},
})
export default class Money extends Vue {

  record: RecordItem = {tags: [], notes: '', type: '-', amount: 0};

  recordTypeList = recordTypeList;

  get recordList() {
    return this.$store.state.recordList;
  }

  created() {
    this.$store.commit('fetchRecords');
  }

  onUpdateNotes(value: string) {
    this.record.notes = value;
  }

  onUpdateAmount(value: string) {
    this.record.amount = parseFloat(value);
  }

  saveRecord() {
    if (!this.record.tags || this.record.tags.length === 0) {
      return window.alert('请至少选择一个标签');
    }
    this.$store.commit('createRecord', this.record);
    if (this.$store.state.createRecordError === null) {
      window.alert('已保存');
      this.record.notes = '';
    }
  }

}
</script>

<style lang="scss" scoped>
@import "./src/assets/style/helper";
::v-deep .layout-content {
  display: flex;
  flex-direction: column-reverse;
}

.notes {
  position: absolute;
}

</style>
