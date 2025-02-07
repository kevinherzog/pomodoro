<template>
    <div>{{ timerCount }}</div>
    <button v-on:click="playpause">Play/Pause</button>
</template>

<script>
    import {ref} from 'vue'
    const worktime = ref(30)
    export default {
        data() {
            return {
                paused: true,
                timerCount: worktime
            }
        },
        watch: {
            paused(value) {
                if (value) {
                    setTimeout(() => {
                        this.timerCount--;
                    }, 1000);
                }
            },

            timerCount: {
                handler(value) {
                    if (value > 0 && this.paused) {
                        setTimeout(() => {
                            this.timerCount--;
                        }, 1000);
                    }
                },
                immediate: true // This ensures the watcher is triggered upon creation
            }

        },
            methods: {
                playpause(){
                    this.paused = !this.paused
                }
            }
        }
</script>