<template>
    <div>{{ timerCount }}</div>
    <button v-on:click="playpause">Play/Pause</button>
    <button v-on:click="skip">Skip</button>

</template>

<script>
import { computed, ref } from 'vue'
const workTime = ref(10)
const chillTime = ref(5)
const longChillTime = ref(15)
const round = ref(4)
export default {
    data() {
        return {
            relaxTime: false,
            running: false,
            timerCount: workTime.value,
            alarm: new Audio('/alarm.mp3'),
            intervallId: null
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
            console.log(this.running)
            if (this.running) {
                this.stopTimer()
            }else{
                this.startTimer()
            }
        },
        breakTime() {
            this.relaxTime = true
            this.running = false
            if (round.value % 4 == 0) { // Check if time for big break
                console.log('long breaky time')
                this.timerCount = longChillTime.value
            } else {
                this.timerCount = chillTime.value
            }
        },
        workyTime() {
            this.relaxTime = false;
            this.running = false
            this.timerCount = workTime.value
        },
        timeEndHandler() {
            // this.alarm.play()
            if (this.relaxTime) {
                round.value++
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