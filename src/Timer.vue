<template>
    <div>{{ timerCount }}</div>
    <button v-on:click="playpause">Play/Pause</button>
    <button v-on:click="skip">Skip</button>

</template>

<script>
import { ref } from 'vue'
export default {
    data() {
        return {                           
        timerCount: 10,
        workTimeValue: 10,
        chillTimeValue: 5,
        longChillTimeValue: 15,
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
            // this.alarm.play()
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
        }
    }
}
</script>