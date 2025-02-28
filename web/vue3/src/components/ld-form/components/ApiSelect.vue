<template>
  <el-select
    :loading="loading"
    @visible-change="handleFetch"
    v-model:value="state"
    v-bind="$attrs"
  >
    <el-option
      v-for="(item, index) in getOptions"
      :key="item.value + index"
      :label="item.label"
      :value="item.value"
      :disabled="item.disabled"
    ></el-option>
  </el-select>
</template>
<script>
import { computed, defineComponent, ref, unref, watchEffect } from "vue";
import VueTypes from "vue-types";
import { isFunction, isStringArray } from "@/landao/utils/is";
import { useAttrs } from "../hooks/useAttrs";
import { useRuleFormItem } from "../hooks/useRuleFormItem";

export default defineComponent({
  name: "ApiSelect",
  props: {
    value: [Array, Object, String, Number],
    api: {
      type: Function,
      default: null,
    },
    labelField: VueTypes.string.def("label"), //下拉数组项内label显示文本的字段
    valueField: VueTypes.string.def("value"), //下拉数组项内value实际值的字段
    disabledField: VueTypes.string.def("disabled"), //下拉数组项内是否禁用该选项
    params: VueTypes.object.def({}), //接口请求参数
    immediate: VueTypes.bool.def(true), //是否立即请求接口
  },
  setup(props) {
    //el-select loading
    const loading = ref(false);
    //是否已初次请求过api,防止重新请求
    const isFirstLoad = ref(true);
    //选项值
    const options = ref([]);
    //select props
    const attrs = useAttrs();

    const [state] = useRuleFormItem(props);

    //获取并重置 options
    const getOptions = computed(() => {
      const { labelField, valueField, disabledField } = props;
      const apiOptions = unref(options);

      if (apiOptions?.length <= 0) {
        return [];
      }
      //判断数组中是否是字符串值
      const isStringArr = isStringArray(apiOptions);
      return apiOptions.map((item) => ({
        label: !isStringArr ? item[labelField] : item,
        value: !isStringArr ? item[valueField] : item,
        disabled: !isStringArr
          ? item[disabledField]
            ? !!item[disabledField]
            : false
          : false,
      }));
    });

    //获取远程数据
    async function fetch() {
      const { api: apiService, params } = props;
      if (!apiService || !isFunction(apiService)) return;
      try {
        loading.value = true;
        const result = await apiService(params);
        options.value = result.data;
      } catch (error) {
        console.error("ApiSelect:apiService:error", error);
      } finally {
        loading.value = false;
        isFirstLoad.value = false;
      }
    }

    watchEffect(() => {
      //立即请求且已经请求过，如果不进行是否已经请求过，会出现无限次的请求
      props.immediate && unref(isFirstLoad) && fetch();
    });

    //手动触发
    async function handleFetch() {
      //判断是否立即请求和是否已经请求过
      if (!props.immediate && unref(isFirstLoad)) {
        await fetch();
      }
    }

    return {
      loading,
      handleFetch,
      attrs,
      state,
      getOptions,
    };
  },
});
</script>
