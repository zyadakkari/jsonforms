<template>
  <div>
    <img alt="Vue logo" src="./assets/logo.png" />
    <h1>JSON Forms Vue 3</h1>
    <div v-if="schema" class="myform">
      <json-forms
        :data="data"
        :renderers="renderers"
        :schema="schema"
        :uischema="uischema"
        @change="onChange"
      />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { JsonForms, JsonFormsChangeEvent } from "@jsonforms/vue";
import {
  defaultStyles,
  mergeStyles,
  vanillaRenderers,
} from "@jsonforms/vue-vanilla";
import { data } from "./components/data.js";

// mergeStyles combines all classes from both styles definitions into one
const myStyles = mergeStyles(defaultStyles, {
  control: { label: "mylabel", input: "myInput" },
});

const renderers = [
  ...vanillaRenderers,
  // here you can add custom renderers
];

export default defineComponent({
  name: "App",
  components: {
    JsonForms,
  },
  data() {
    return {
      // freeze renderers for performance gains
      renderers: Object.freeze(renderers),
      schema: null as any,
      uischema: {} as any,
      data: data,
    };
  },
  async mounted() {
    try {
      const response = await fetch(
        "https://toucanapp-imgs-public.s3.eu-west-2.amazonaws.com/schema.json"
      );
      this.schema = await response.json();
      if (this.schema) {
        this.uischema = {
          type: "VerticalLayout",
          elements: Object.keys(this.schema.properties).map((property) => ({
            type: "Control",
            scope: `#/properties/${property}`,
          })),
        };
      }
    } catch (error) {
      console.error("Failed to load data:", error);
    }
  },
  methods: {
    onChange(event: JsonFormsChangeEvent) {
      this.data = event.data;
    },
  },
  provide() {
    return {
      styles: myStyles,
    };
  },
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  margin-left: 120px;
  margin-right: 120px;
}
.mylabel {
  background-color: red;
}
.myInput {
  background-color: pink;
}
.myButton {
  background-color: green;
}
</style>
