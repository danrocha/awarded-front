<template functional>
  <router-link
    :to="`/movies/award/${props.awardNameShort}/category/${props.categoryName}/prize/${props.prizeName}`"
    tag="a"
    class="title-link"
    :class="[data.class, data.staticClass]"
    :style="[data.style, data.staticStyle]"
    v-bind="data.attrs"
    v-on="listeners"
    ><slot>{{ props.prizeName }}</slot></router-link
  >
</template>
<script>
export default {
  props: {
    categoryName: {
      type: String,
      required: true
    },
    awardNameShort: {
      type: String,
      required: true
    },
    prizeName: {
      type: String,
      required: true
    }
  }
};
</script>
