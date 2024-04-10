<script setup>
import { ref, reactive } from 'vue'
import carPng from '../assets/car.png'
import bikePng from '../assets/bike.png'

const SPEED = 0.15
defineProps({
  msg: String,
})
//20个站点
const stations = reactive([
  { id: "站点0", count: 120, x: 16.5, y: 10.5 },
  { id: "站点1", count: 120, x: 4.5, y: 3 },
  { id: "站点2", count: 120, x: 8, y: 3.8 },
  { id: "站点3", count: 120, x: 18.1, y: 21.3 },
  { id: "站点4", count: 120, x: 11.4, y: 3.7 },
  { id: "站点5", count: 120, x: 2.3, y: 8.55 },
  { id: "站点6", count: 120, x: 4.23, y: 11.15 },
  { id: "站点7", count: 120, x: 7, y: 10.55 },
  { id: "站点8", count: 120, x: 1.9, y: 15.22 },
  { id: "站点9", count: 120, x: 1.5, y: 22.7 },
  { id: "站点10", count: 120, x: 5.5, y: 17.6 },
  { id: "站点11", count: 120, x: 5.1, y: 27.1 },
  { id: "站点12", count: 120, x: 7.7, y: 30.5 },
  { id: "站点13", count: 120, x: 7.2, y: 23.6 },
  { id: "站点14", count: 120, x: 8.6, y: 18.5 },
  { id: "站点15", count: 120, x: 11.1, y: 14.2 },
  { id: "站点16", count: 120, x: 13.8, y: 19.2 },
  { id: "站点17", count: 120, x: 10.5, y: 24.6 },
  { id: "站点18", count: 120, x: 26, y: 18.5 },
  { id: "站点19", count: 120, x: 21.3, y: 14.4 },
]
)
//4辆调度车
const cars = reactive([
  { id: "车辆1", x: 1, y: 1, count: 0, from: 10, to: 0, load: 30, speedX: 0, speedY: 0 },
  { id: "车辆2", x: 8, y: 3.8, count: 0, from: 12, to: 2, load: 30, speedX: 0, speedY: 0 },
  { id: "车辆3", x: 18.1, y: 21.3, count: 0, from: 8, to: 19, load: 100, speedX: 0, speedY: 0 },
  { id: "车辆4", x: 11.4, y: 3.7, count: 0, from: 17, to: 1, load: 20, speedX: 0, speedY: 0 }
]
)

function move() {
  cars.forEach(car => {
    if (car.load !== 0) {
      //如果需要装载或者卸载 到达From站点
      const station = stations[car.from]
      const stationX = station.x * 20
      const stationY = station.y * 20

      //如果已经到达站点
      if (Math.abs(car.x - station.x) < 2 && Math.abs(car.y - station.y) < 2) {
        //进行装卸
        car.count += 1
        station.count -= 1
        car.load -= 1
        car.sppedX = 0
        car.speedY = 0
        return
      }
      //计算车辆移动方向
      if (car.speedX !== 0 && car.speedY !== 0) {
        car.x += car.speedX
        car.y += car.speedY
        return
      }
      const dx = stationX - car.x * 20
      const dy = stationY - car.y * 20
      const distance = Math.sqrt(dx * dx + dy * dy)
      const speedX = dx / distance * SPEED
      const speedY = dy / distance * SPEED
      car.speedX = speedX
      car.speedY = speedY
      car.x += speedX
      car.y += speedY
    }
    //需要到达TO站点
    else {
      const station = stations[car.to]
      const stationX = station.x * 20
      const stationY = station.y * 20
      //如果已经到达站点
      if (Math.abs(car.x - station.x) < 0.2 && Math.abs(car.y - station.y) < 0.2) {
        //原地待命
        return
      }
      //计算车辆移动方向
      if (car.speedX !== 0 && car.speedY !== 0) {
        car.x += car.speedX
        car.y += car.speedY
        return
      }
      const dx = stationX - car.x * 20
      const dy = stationY - car.y * 20
      const distance = Math.sqrt(dx * dx + dy * dy)
      const speedX = dx / distance * SPEED
      const speedY = dy / distance * SPEED
      car.speedX = speedX
      car.speedY = speedY
      car.x += speedX
      car.y += speedY
    }
    // debugger
  })
  // debugger

  setTimeout(() => {
    move()
  }, 30)

}



</script>

<template>
  <div class="card">
    <button type="button" @click="move">开始调度</button>
  </div>
  <div
    style="width: 600px; height: 730px;background-image:url('src/assets/map.png'); background-repeat: no-repeat;background-size: contain ; position: relative;border: 1px solid black; ">
    <div v-for="car in cars" :style="{ top: car.y * 20 + 'px', left: car.x * 20 + 'px' }"
      style="position: absolute; z-index: 100;display: flex;justify-content: center;align-items: center;flex-flow: column;">
      <img v-if="car.count !== 0" :src="bikePng"
        style="width: 30px;height: 20px; position: absolute; left: 12px; top:17px;"></img>

      <img :src="carPng" style="width: 70px;height: 70px;"></img>

      <div style="font-size: 12px; color:white ;background-color: lightcoral;margin-top: -10px;">
        {{ car.id }}: {{ car.count }}

      </div>
    </div>

    <div v-for="station in stations" :style="{ top: station.y * 20 - 2 + 'px', left: station.x * 20 - 25 + 'px' }"
      :key="station.id" style="display: flex;font-weight: bold;justify-content: center;align-items: center;flex-flow: column;position:absolute;
      width:100px;">

      <div style=" font-size: 14px;">{{ station.id }}: <p style="font-size: 14px;margin:0px;display: inline-block;">{{
        station.count }}</p>
      </div>

      <div style="width: 10px;height: 10px;background-color: black;"></div>

      <!-- <img :src="bikePng" style="width: 30px;height: 20px;" ></img> -->

    </div>

  </div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}

#simulation {
  position: relative;
  width: 400px;
  height: 400px;
  border: 1px solid black;
}

.station {
  border-radius: 50%;
  text-wrap: nowrap;
  position: absolute;
  width: 30px;
  height: 30px;
  background-color: lightblue;
  text-align: center;
  line-height: 100px;
}


.dispatch-car {
  border-radius: 50%;
  position: absolute;
  width: 50px;
  height: 50px;
  background-color: lightgreen;
  text-align: center;
  line-height: 50px;
}

#car-1 {
  top: 150px;
  left: 250px;
}

#car-2 {
  top: 150px;
  left: 300px;
}
</style>
