<template>
  <div>

    <!-- select view -->
    <div>
      <el-form :inline="true" size="small">
        <el-form-item>
          <el-select v-model="selectedView" placeholder="View As">
            <el-option
              v-for="item in views"
              :key="item.value"
              :label="$t(item.text)"
              :value="item.value">
            </el-option>
            <span slot="prefix" class="fa fa-sitemap"></span>
          </el-select>
        </el-form-item>
      </el-form>
    </div>

    <!-- content show -->
    <div>
      <component :is="selectedView" :data="$data" :redisKey='redisKey'></component>
    </div>

  </div>
</template>

<script>
import unserialize from 'locutus/php/var/unserialize';
import StringViewText from '@/components/StringViewText';
import StringViewJson from '@/components/StringViewJson';
import StringViewPhpUnserialize from '@/components/StringViewPhpUnserialize';

export default {
  data() {
    return {
      selectedView: 'StringViewText',
      views: [
        { value: 'StringViewText', text: 'message.view_as_text' },
        { value: 'StringViewJson', text: 'message.view_as_json' },
        { value: 'StringViewPhpUnserialize', text: 'message.view_as_phpunserialize' },
      ],
      content: '',
      newKeyParamsReference: this.newKeyParams,
    };
  },
  props: ['redisKey', 'newKeyMode', 'newKeyParams'],
  components: { StringViewText, StringViewJson, StringViewPhpUnserialize },
  methods: {
    initShow() {
      if (this.newKeyMode) {
        return;
      }

      const key = this.redisKey;
      const client = this.$util.get('client');

      if (!key) {
        return;
      }

      client.getAsync(key).then((reply) => {
        // character not visible
        if (!this.$util.isVisible(reply)) {
          this.content = this.$util.toUTF8(reply);
        }

        else {
          this.content = reply;
        }
      });
    },
  },
  mounted() {
    this.initShow();
  },
};
</script>

<style type="text/css">
  .text-formated-container {
    border: 1px solid #dcdfe6;
    min-height: 88px;
    padding: 5px 15px;
    line-height: 1.5;
    border-radius: 5px;
  }

  .vjs__tree span {
    color: #616069;
  }

  .collapse-container {
    height: 27px;
  }

  .collapse-container .collapse-btn {
    float: right;
    padding: 9px 0;
  }
</style>
