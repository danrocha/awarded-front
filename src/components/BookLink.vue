<template functional>
  <router-link
    :to="`/books/${$options.methods.cleanTitle(props.bookTitle)}/${props.bookId}`"
    tag="a"
    class="title-link"
    :title="bookTitle"
    :class="[data.class, data.staticClass]"
    :style="[data.style, data.staticStyle]"
    v-bind="data.attrs"
    v-on="listeners"
    ><span
      ><slot>{{ props.bookTitle }}</slot></span
    ></router-link
  >
</template>
<script>
export default {
  props: {
    bookId: {
      type: Number,
      required: true
    },
    bookTitle: {
      type: String,
      required: true
    }
  },
  methods: {
    cleanTitle(title) {
      //remove everything after a colon
      if (title.indexOf(":") > 1) {
        title = title.slice(0, title.indexOf(":"));
      }
      //remove spaces and punctuation, making everything lowercase and split by dashes
      return title
        .toLowerCase()
        .replace(/[^\w ]+/g, "")
        .replace(/ +/g, "-");
    }
  }
};
</script>
