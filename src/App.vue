<template>
  <div>
    <img class="block mx-auto" alt="Vue logo" src="./assets/logo.png" />
    <h1 class="font-bold">JSON Forms Vue 3</h1>
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
import { JsonForms } from "@jsonforms/vue";
import {
  defaultStyles,
  mergeStyles,
  vanillaRenderers,
} from "@jsonforms/vue-vanilla";
import { data } from "./components/data";
// mergeStyles combines all classes from both styles definitions into one
const myStyles = mergeStyles(defaultStyles, {
  control: {
    label:
      "block w-full pb-1 text-md font-bold text-left font-medium text-gray-500 transition-all duration-200 ease-in-out",
    input:
      "h-10 w-full rounded-md bg-gray-50 px-4 font-thin outline-none drop-shadow-sm transition-all duration-200 ease-in-out",
    select: "appearance-none w-full py-1 px-2 bg-gray-50 rounded-md text-black",
    root: "mt-5 text-[#121826]",
    error: "text-red-500 text-start mt-1",
    description: "text-start mt-1",
  },
  arrayList: {
    legend:
      "w-full bg-white flex-row-reverse bg-white flex justify-between items-center  border-0",
    label: "text-lg font-semibold text-[#121826]",
    addButton: "text-3xl font-medium",
    itemWrapper: "p-5 border-2 rounded-lg mb-3 text-[#121826]",
    itemLabel:
      "flex-1 w-fit h-fit text-start font-medium text-[18px] !bg-transparent",
    itemToolbar: "flex",
    itemMoveUp:
      "ml-auto py-1 px-2 disabled:bg-[#aaa] text-[14px] bg-black text-white rounded-lg size-[30px]",
    itemMoveDown:
      "py-1 px-2 disabled:bg-[#aaa] text-[14px] bg-black text-white rounded-lg mx-2 size-[30px]",
    itemDelete:
      "py-1 px-2 disabled:bg-[#aaa] text-[14px] bg-black text-white rounded-lg size-[30px]",
  },
  group: {
    label: "font-bold text-2xl mb-3",
    root: "",
  },
  horizontalLayout: {
    root: "gap-4",
    item: "",
  },
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
      const schemaData = await response.json();
      this.schema = schemaData;

      if (this.schema) {
        this.uischema = require("./components/uiSchema.json");
        // this.uischema = {
        //   type: "VerticalLayout",
        //   elements: Object.keys(this.schema.properties).map((property) => ({
        //     type: "Control",
        //     scope: `#/properties/${property}`,
        //   })),
        // };
      }
    } catch (error) {
      console.error("Failed to load data:", error);
    }
  },
  updated() {
    const controlElements = document.querySelectorAll(".control");

    controlElements.forEach((controlElement) => {
      if (
        (controlElement.id && controlElement.id.includes("language_tag")) ||
        controlElement.id.includes("marketplace_id")
      ) {
        (controlElement as HTMLElement).style.display = "none";
      }
    });
  },
  methods: {
    onChange(event: any) {
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
  font-family: sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
  margin-left: 120px;
  margin-right: 120px;
}
/* Define custom styles for JSON Forms components */
.myform {
  max-width: 1200px;
  padding: 0 20px;
  margin: 0 auto;
}
.horizontal-layout-item {
  padding: 10px 10px;
}
</style>
