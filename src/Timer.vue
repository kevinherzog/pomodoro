

<template>
    <div class="place-self-center text-3xl text-white">
        <h1 v-if="!relaxTime">Worky Time</h1>
        <h1 v-else>Breaky Time</h1>
    </div>
    <div class="text-9xl place-self-center text-white my-20">{{ minutes }}:{{ seconds.toString().padStart(2, '0') }}</div>
    <div class="grid grid-cols-4 gap-2 w-96 place-self-center">
            <button class="btn col-span-2" v-on:click="playpause">Play/Pause</button>
            <button class="btn col-span-2" v-on:click="skip">Skip</button>
            <button class="btn" v-on:click="subFiveMin">-5</button>
            <button class="btn" v-on:click="subOneMin">-1</button>
            <button class="btn" v-on:click="addOneMin">+1</button>
            <button class="btn" v-on:click="addFiveMin">+5</button>
    </div>

</template>

<script>
export default { 
    data() {
        return {  
            minuteLength: 60,                      
            workTimeValue: (60 * 25),
            timerCount: (60 * 25)  ,
            chillTimeValue: (60 * 5),
            longChillTimeValue: (60 * 15) ,
            round: 1,
            relaxTime: false,
            running: false,
            intervalId: null, 
            alarm: new Audio('/alarm.mp3'),
        }
    },
    methods: {
        startTimer() {
            if (this.intervalId) return; // Prevent duplicate intervals

            this.running = true;
            this.intervalId = setInterval(() => {
                if (this.timerCount > 0) {
                    this.timerCount--;
                } else {
                    this.timeEndHandler();
                    clearInterval(this.intervalId);
                    this.intervalId = null; // Reset interval ID
                }
            }, 1000);
        },

        stopTimer() {
            this.running = false;
            clearInterval(this.intervalId);
            this.intervalId = null; // Reset interval ID
        },
        playpause() {
            if (this.running) {
                this.stopTimer()
            }else{
                this.startTimer()
            }
        },
        breakTime() {
            console.log(this.round)
            this.relaxTime = true
            this.running = false
            if (this.round % 4 == 0) { // Check if time for big break
                console.log('long breaky time')
                this.timerCount = this.longChillTimeValue
            } else {
                this.timerCount = this.chillTimeValue
            }
        },
        workyTime() {
            this.relaxTime = false;
            this.running = false;
            this.timerCount = this.workTimeValue
        },
        timeEndHandler() {
            this.alarm.play()
            this.stopTimer()
            if (this.relaxTime) {
                this.round++
                this.workyTime()
            } else {
                this.breakTime()
            }
        },
        skip() {
            this.timeEndHandler()
        },
        addOneMin(){
            if (this.relaxTime && (this.round % 4 == 0)) {
                this.longChillTimeValue = this.longChillTimeValue + this.minuteLength
            }else if (this.relaxTime){
                this.chillTimeValue = this.chillTimeValue + this.minuteLength
            }else{
                this.workTimeValue = this.workTimeValue +this.minuteLength 
            }
            this.timerCount = this.timerCount +this.minuteLength
        },
        addFiveMin(){
            if (this.relaxTime && (this.round % 4 == 0)) {
                this.longChillTimeValue = this.longChillTimeValue + 5*this.minuteLength
            }else if (this.relaxTime){
                this.chillTimeValue = this.chillTimeValue + 5*this.minuteLength
            }else{
                this.workTimeValue = this.workTimeValue + 5*this.minuteLength 
            }
            this.timerCount = this.timerCount + 5*this.minuteLength
        },
        subFiveMin() {
            if (this.timerCount < 5*this.minuteLength+1) {
                if (this.relaxTime && (this.round % 4 == 0)) {
                    this.longChillTimeValue = 0
                }else if (this.relaxTime){
                    this.chillTimeValue = 0
                }else{
                    this.workTimeValue = 0 
                }
                this.timerCount = 0
            }else{
                if (this.relaxTime && (this.round % 4 == 0)) {
                    this.longChillTimeValue = this.longChillTimeValue - 5*this.minuteLength
                }else if (this.relaxTime){
                    this.chillTimeValue = this.chillTimeValue - 5*this.minuteLength
                }else{
                    this.workTimeValue = this.workTimeValue - 5*this.minuteLength 
                }
                this.timerCount = this.timerCount  - 5*this.minuteLength
            }
        },
        subOneMin() {
            if (this.timerCount < this.minuteLength+1) {
                if (this.relaxTime && (this.round % 4 == 0)) {
                    this.longChillTimeValue = 0
                }else if (this.relaxTime){
                    this.chillTimeValue = 0
                }else{
                    this.workTimeValue = 0 
                }
                this.timerCount = 0
            }else{
                if (this.relaxTime && (this.round % 4 == 0)) {
                    this.longChillTimeValue = this.longChillTimeValue - this.minuteLength
                }else if (this.relaxTime){
                    this.chillTimeValue = this.chillTimeValue - this.minuteLength
                }else{
                    this.workTimeValue = this.workTimeValue - this.minuteLength 
                }
                this.timerCount = this.timerCount - this.minuteLength
            }
        }
    },  
    computed: {
        minutes() {
            return Math.floor(this.timerCount / 60);
        },
        seconds() {
            return this.timerCount % 60;
        },
        bgColor(){
            return (this.relaxTime ? "bg-slate-700":"bg-red-950")
        }
  },
  watch: {
    bgColor(newBg, oldBG){
        document.body.classList.remove(oldBG);
        document.body.classList.add(newBg);
        console.log(newBg)
        console.log(oldBG)
    }
  },
  mounted() {
    document.body.classList.add(this.bgColor);
  },
  beforeUnmount() {
    document.body.classList.remove(this.bgColor); 
  }
}
</script>