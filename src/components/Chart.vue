<template>
  <div class="grid grid-cols-1">
    <div class="text-left my-4">
      <p class="text-black font-medium text-xl">Chart Show data</p>
    </div>
    <div class="flex flex-wrap">
      <button
        class="mx-2 px-2 h-8 py-1 btn-actioncard w-24"
        :style="
          v.status ? `background-color:${v.color}` : `background-color:white`
        "
        :class="v.status ? `border-none` : `border-gray-400`"
        v-for="(v, k) in dataChart"
        :key="k"
        @click="v.status = !v.status"
      >
        <span
          class="text-sm"
          :class="v.status ? `text-white` : `text-gray-400`"
          >{{ v.name }}</span
        >
        <span>
          <svg
            v-if="v.status"
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
            class="w-3 h-3 fill-white"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M6 18L18 6M6 6l12 12"
            />
          </svg>
          <svg
            v-else
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
            class="w-5 h-5 fill-gray-400"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M12 9v6m3-3H9m12 0a9 9 0 11-18 0 9 9 0 0118 0z"
            />
          </svg>
        </span>
      </button>
    </div>
    <div class="my-4">
      <highcharts class="hc" :options="chartdata" ref="chart"></highcharts>
    </div>
  </div>
</template>
<script>
import { reactive, ref } from "@vue/reactivity";
import { computed, onMounted, watch } from "@vue/runtime-core";
export default {
  name: "Chart",
  setup() {
    const loadData = ref(0);
    const timeChart = reactive([]);
    const dataChart = reactive([
      {
        name: "data 1",
        data: [],
        color: "",
        status: true,
      },
      {
        name: "data 2",
        data: [],
        color: "",
        status: true,
      },
      {
        name: "data 3",
        data: [],
        color: "",
        status: true,
      },
      {
        name: "data 4",
        data: [],
        color: "",
        status: true,
      },
      {
        name: "data 5",
        data: [],
        color: "",
        status: true,
      },
      {
        name: "data 6",
        data: [],
        color: "",
        status: true,
      },
    ]);

    const dataShowchart = reactive([]);

    watch(dataChart, (newData, oldData) => {
      dataShowchart.splice(0);
      var test = newData.filter((e) => e.status == true);
      dataShowchart.push(...test);
    });

    const chartdata = reactive({
      title: {
        text: "",
        align: "left",
      },
      xAxis: {
        categories: computed(() => timeChart),
      },
      yAxis: [
        {
          title: {
            text: "",
          },
        },
        {
          linkedTo: 0,
          gridLineWidth: 0,
          opposite: true,
          title: {
            text: "",
          },
          labels: {
            align: "right",
            x: -3,
            y: 16,
          },
          showFirstLabel: false,
        },
      ],
      legend: {
        enabled: false,
      },

      tooltip: {
        shared: true,
        crosshairs: true,
      },

      plotOptions: {
        series: {
          cursor: "pointer",
          className: "popup-on-click",
          marker: {
            lineWidth: 1,
          },
        },
      },
      series: computed(() => dataShowchart),
    });

    onMounted(() => {
      getData();
    });

    const getData = async () => {
      const { time, loopdata } = await createArrayTimeData();

      timeChart.push(...time);

      for (const items of dataChart) {
        items.color = functionrandomColor();
        items.data.push(...(await functionRandomData(loopdata)));
      }

      loadData.value++;
    };

    const createArrayTimeData = async () => {
      const dateArray = [];
      var timenow = new Date();
      timenow.setMinutes(0, 0, 0);
      var newTime = new Date();
      newTime.setHours(newTime.getHours() + 6);
      var timenext = newTime;
      var countloop = 0;

      while (timenow <= timenext) {
        var timeadd = new Date(timenow).toLocaleTimeString("th-TH", {
          hour: "2-digit",
          minute: "2-digit",
        });

        dateArray.push(`${timeadd}`);
        countloop++;

        timenow.setMinutes(timenow.getMinutes() + 30);
      }

      return {
        time: dateArray,
        loopdata: countloop,
      };
    };

    const functionRandomData = (count) => {
      var arraylist = [];

      for (var i = 0; i < count; i++) {
        arraylist.push(parseFloat(Math.random().toFixed(2)));
      }

      return arraylist;
    };

    const functionrandomColor = () => {
      var randomColor = Math.floor(Math.random() * 16777215).toString(16);

      return "#" + randomColor;
    };

    return {
      chartdata,
      loadData,
      dataChart,
    };
  },
};
</script>
