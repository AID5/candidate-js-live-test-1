<template>
  <div class="flex space-x-2 justify-center">
    <button @click="onClick" type="button" v-bind:class="getClass()">
      <slot>Button</slot>
    </button>
  </div>
</template>

<script>
export default {
  props: {
    onClick: {
      type: Function,
      required: true,
    },
    disable: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
  methods: {
    getClass() {
      const base =
        "mb-2 w-full px-6 py-2.5 text-white font-medium text-xs leading-normal uppercase rounded shadow-md transition duration-150 ease-in-out";
      const classNames = this.disable
        ? "bg-gray-400  pointer-events-none"
        : "bg-gray-500 hover:bg-gray-600 hover:shadow-lg active:shadow-lg";
      return `${base} ${classNames}`;
    },
  },
};
</script>
