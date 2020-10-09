<template>
  <div class="container" :style="pannelLeft">
    <i
      v-if="pannelActive"
      class="fas fa-times curser"
      @click="pannelActiveAction(false)"
    ></i>
    <i
      v-if="!pannelActive"
      class="fas fa-bars curser"
      @click="pannelActiveAction(true)"
    ></i>
    <h2>알림 설정 패널</h2>
    <div class="timer-container">
      <b>시간설정</b>
      <ul>
        <li>
          <label for="hour">시</label>
          <input
            type="number"
            name=""
            v-model="hour"
            id="hour"
            min="0"
            max="24"
          />
        </li>
        <li class="divide">:</li>
        <li>
          <label for="minutes">분</label>
          <input
            type="number"
            name=""
            v-model="minutes"
            id="minutes"
            min="0"
            max="60"
          />
        </li>
      </ul>
    </div>
    <div class="interval-setting-container">
      <b>간격설정(초단위)</b>
      <div>
        <ul>
          <li>
            <input type="number" name="" v-model="termStart" id="termStart" />
          </li>
          <li class="pa-horizontal-10">~</li>
          <li>
            <input type="number" name="" v-model="termEnd" id="termEnd" />
          </li>
        </ul>
      </div>
    </div>
    <div class="schedule-list-container">
      <b>스케쥴 리스트</b>
      <hr />
      <p class="mt20 bold from-time" v-text="fromTimerView"></p>
      <p
        class="mt20 bold from-time"
        style="font-size:16px"
        v-text="fromTermTimeView"
      ></p>
      <ul></ul>
      <div class="mt20">
        <button
          v-if="!isStart"
          class="btn start-btn curser"
          @click="startAlarm()"
        >
          <i class="far fa-clock"></i> 시작
        </button>
        <button v-if="isStart" class="btn start-btn curser" @click="finish()">
          <i class="far fa-pause-circle"></i> 정지
        </button>
        <button class="btn reset-btn curser" @click="resetAlarm()">
          <i class="fas fa-undo-alt"></i> 리셋
        </button>
        <button class="btn save-btn curser" @click="saveStorageAlarm()">
          <i class="far fa-save"></i> 저장
        </button>
        <button class="btn remove-btn curser" @click="removeStorageAlarm()">
          <i class="far fa-trash-alt"></i> 삭제
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      pannelActive: true,
      hour: 0,
      minutes: 0,
      isStart: false,
      intervalId: "",
      termStart: 5,
      termEnd: 10
    };
  },
  mounted: function() {
    this.init();
  },
  computed: {
    pannelLeft: function() {
      return this.pannelActive ? "left : 0px" : "left:-250px";
    },
    fromTimerView: function() {
      return this.hour + ":" + this.minutes + "분으로 부터";
    },
    fromTermTimeView: function() {
      return this.termStart + "초부터 " + this.termEnd + "초 사이 랜덤초 적용 ";
    }
  },
  methods: {
    getNextSeconds: function() {
      return (
        Math.floor(Math.random() * (this.termEnd - this.termStart)) +
        this.termStart
      );
    },
    isAfterStartTime: function() {
      let currentTime = new Date();
      let alarmStartTime = new Date(
        currentTime.getFullYear(),
        currentTime.getMonth(),
        currentTime.getDate(),
        this.hour,
        this.minutes
      );
      return alarmStartTime <= currentTime;
    },
    run: function() {
      if (this.isStart && this.isAfterStartTime()) {
        this.$emit("actionalarm");
        let time = this.getNextSeconds();
        setTimeout(this.run, time * 1000);
      } else {
        setTimeout(this.run, 1000);
      }
    },
    finish: function() {
      this.isStart = false;
    },
    init: function() {
      const json = localStorage.getItem("MyAlarm");
      const obj = JSON.parse(json);
      if (obj == null) {
        this.hour = new Date().getHours();
        this.minutes = new Date().getMinutes();
      } else {
        this.hour = obj.time.hour;
        this.minutes = obj.time.minutes;
        this.termStart = obj.time.termStart;
        this.termEnd = obj.time.termEnd;
      }
    },
    pannelActiveAction: function(ck) {
      this.pannelActive = ck;
    },
    startAlarm: function() {
      this.isStart = true;
      this.run();
    },
    resetAlarm: function() {
      if (this.isStart) {
        alert(
          "알림 스케쥴 머신이 가동중입니다.\n 중지 하고 리셋 할 수 있습니다."
        );
      }
      if (
        !this.isStart &&
        confirm("셋팅된 스케쥴 리스트를 초기화 하겠습니까?")
      ) {
        this.init();
      }
    },
    saveStorageAlarm: function() {
      if (
        confirm(
          "위 설정값들을 스토리지에 저장하겠습니까?\n(저장시 다음 접속시에도 설정된 값으로 초기 설정이 됩니다.)"
        )
      ) {
        let myAlarm = {
          time: {
            hour: this.hour,
            minutes: this.minutes,
            termStart: this.termStart,
            termEnd: this.termEnd
          }
        };
        localStorage.setItem("MyAlarm", JSON.stringify(myAlarm));
        return;
      }
    },
    removeStorageAlarm: function() {
      if (
        confirm(
          "스토리지에 저장된 데이터를 삭제하겠습니까?\n(삭제시 다음 접속시 불러올 데이터가 없어지게 됩니다, 다시 셋팅 필요.)"
        )
      ) {
        localStorage.removeItem("MyAlarm");
        this.init();
      }
    }
  }
};
</script>

<style>
@import "../assets/css/components/setting-pannel.css";
</style>
