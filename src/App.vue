<template>
  <div>
    <img class="block mx-auto" alt="Vue logo" src="./assets/logo.png" />
    <h1 class="font-bold mb-5">JSON Forms Vue 3</h1>
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
import schemaJson from "./components/uiSchema.json";
// mergeStyles combines all classes from both styles definitions into one
const myStyles = mergeStyles(defaultStyles, {
  control: {
    label:
      "block w-full pb-1 text-md font-bold text-left font-bold text-[#121826]",
    input: "border border-gray-300 shadow px-3 py-2 w-full rounded-md",
    select:
      "appearance-none border border-gray-300 shadow px-3 py-2 w-full rounded-md text-[#121826]",
    root: "text-[#121826]",
    error: "text-red-500 text-start mt-1",
    description: "text-start text-sm my-1 !min-h-fit",
  },
  arrayList: {
    legend:
      "w-full bg-white flex-row-reverse bg-white flex justify-between items-center  border-0",
    label: "text-[16px] font-semibold text-[#121826]",
    addButton: "text-[28px] font-medium",
    itemWrapper: "p-3 border-2 rounded-lg mb-3 text-[#121826]",
    itemLabel:
      "flex-1 w-fit h-fit text-start font-medium text-[16px] !bg-transparent line-clamp-1",
    itemToolbar: "flex ",
    itemMoveUp:
      "ml-auto py-1 mr-1 px-2 text-black disabled:hidden text-[14px] bg-gray-200 hover:bg-gray-300 duration-300 rounded-lg size-[30px]",
    itemMoveDown:
      "py-1 px-2 mr-1 text-black disabled:hidden text-[14px] bg-gray-200 hover:bg-gray-300 duration-300 rounded-lg size-[30px]",
    itemDelete:
      "py-1 px-2 disabled:hidden text-[14px] rounded-lg bg-red-600 text-red-100 hover:bg-red-700 duration-300 size-[30px]",
    itemExpanded: "[&>.array-list-item-label]:line-clamp-6",
  },
  group: {
    label: "font-bold text-[20px] underline mb-3",
    root: "",
  },
  horizontalLayout: {
    root: "gap-5",
    item: "px-5 py-3 mb-5 shadow-md border-2 rounded-xl",
  },
});

const renderers = [...vanillaRenderers];

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

      // const c = JSON.parse(localStorage.getItem("schemaData") as string);
      this.schema = schemaData;

      if (this.schema) {
        this.uischema = schemaJson;
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
    const imgs = document.querySelectorAll(".array-list-item-label");
    imgs.forEach((controlElement) => {
      if (
        controlElement.innerHTML &&
        controlElement.innerHTML.includes("images")
      ) {
        (
          controlElement as HTMLElement
        ).innerHTML = `<img src=${controlElement.innerHTML} width='100px'/>`;
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
</style>
