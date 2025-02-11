<template>
    <div>{{ timerCount }}</div>
    <button v-on:click="playpause">Play/Pause</button>
    <button v-on:click="skip">Skip</button>

</template>

<script>
    import {computed, ref} from 'vue'
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
        watch: {
            running(value) {
                if (value) {
                    setTimeout(() => {
                        this.timerCount--;
                    }, 1000);
                }
            },

            timerCount: {
                handler(value) {
                    if (value > 0 && this.running) {
                        setTimeout(() => {
                            this.timerCount--;
                            if (value == 1) {
                                this.timeEndHandler();
                            }
                        }, 1000);
                    }
                },
                immediate: true // This ensures the watcher is triggered upon creation
            }

        },
            methods: {
                startTimer() {
                    this.intervalId = setInterval(() => {
                        if (this.timerCount > 0) {
                        this.timerCount--
                        } else {
                        clearInterval(this.intervalId)
                        this.timeEndHandler()
                        }
                    }, 1000)
                },
                stopTimer(){

                },
                playpause(){
                    this.running = !this.running
                },
                breakTime(){
                    this.relaxTime = true
                    this.running = false
                    console.log(round)
                    if (round.value % 4 == 0) { // Check if time for big break
                        console.log('long breaky time')
                        this.timerCount = longChillTime.value
                    }else{
                        this.timerCount = chillTime.value
                    }
                },
                workyTime(){
                    this.relaxTime = false;
                    this.running = false
                    this.timerCount = workTime.value
                },
                timeEndHandler(){
                    this.alarm.play()
                    if (this.relaxTime) {
                        round.value++
                        this.workyTime()
                    }else{
                        this.breakTime()
                    }
                },
                skip(){
                    this.timeEndHandler()
                }
            }
        }
</script>