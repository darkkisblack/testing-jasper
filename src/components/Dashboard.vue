<template>
  <div class="slidecontainer">
    <p></p>
    <input type="range" min="1" max="4" value="3" class="calendarRange" ref="calendarRange">
    <p>Value: <span ref="calendarValue"></span></p>
  </div>

  <div id="container"></div> 
</template>

<script setup>
import { ref, onMounted } from 'vue'
const calendarRange = ref(null);
const calendarValue = ref(null);
const datesStrings = ["DAY", "WEEK", "MONTH", "YEAR"];
onMounted(() => {
  console.log('calendarRange: ', calendarRange)
  console.log('calendarValue: ', calendarValue)

  calendarValue.innerHTML = datesStrings[parseInt(calendarRange.value.value) - 1];

  calendarRange.value.oninput = function() {
    calendarValue.innerHTML =  datesStrings[parseInt(this.value) - 1];
  }

  let test = null;

  visualize(
    {
      server: "https://172.16.1.17:8443/jasperserver-pro",
      auth: {
        name: "demo",
        password: "demo"
      }
    },
    function (v) { 
      renderDashboard();
      
      calendarRange.value.addEventListener("input", function() {
        renderDashboard();
      });
      
      function renderDashboard(){
        const initialParams = {
          "time_1": [datesStrings[parseInt(calendarRange.value.value) - 1]]
        };

        v("#container").dashboard({ 
          server: "https://172.16.1.17:8443/jasperserver-pro",	
          resource: "/public/ITSStats", 
          params: initialParams,
          linkOptions: {
            beforeRender: function (linkToElemPairs) {
              console.log('linkToElemPairs: ', linkToElemPairs);
              linkToElemPairs.forEach(showCursor);
            },
            events: {
              'click': function(event, link) {
                console.log('event: ', event);
                console.log('link: ', link) // здесь undefined 
                if (link.type === 'ReportExecution') { // пример из видоса
                  console.log('link.parameters: ', link.parameters)
                  test = link;
                  console.log('test: ', test);
                }
              }
            },
          },
          error: function(e) { 
            alert(e); 
          } 
        });
      };

      function showCursor(pair){
        let el = pair.element;
            el.style.cursor = "pointer";
      };
    }
  );
})

</script>

<style lang="scss">
#container {
  width:100%;
  height: 75vh;

  &>.dashboardCanvas {
    &>.content {
      &>.body {
        background-color: transparent!important; //TODO

        .dashletContent {
          border: none!important;
          border-radius: 16px;
          background-color: #1B1E21;
          padding: 24px;

          &>.content {
            background-color: transparent;

            p {
              color: #9DA6B2!important; //TODO
            }

            svg {
              rect {
                &.highcharts-background {
                  fill: transparent;
                }

                &.highcharts-point {
                  rx: 5;
                  // ry: 5;
                }
              }

              rect, path {
                stroke: transparent!important; //TODO
              }

              text {
                color: #DADFE5!important; //TODO
                fill: #DADFE5!important; //TODO
              }
            }
          }
        }
      }
    }

    .dashletToolbar .button {
      outline: none;
    }
  }
} 
</style>
