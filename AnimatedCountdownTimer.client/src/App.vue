<template>
    <header>
        <Navbar />
    </header>
    <main>
        <div class="background">
            <div class="elapsed" :style="{ height: backgroundHeight, backgroundColor }" v-if="timeLeft > 0">
            </div>
        </div>
        <AppTimer :elapsed="timeElapsed" :limit="timeLimit" />

        <router-view />
    </main>
    <footer class="bg-dark text-light">
        Made with 💖 by CodeWorks
    </footer>
</template>

<script>
import { computed } from 'vue'
import { AppState } from './AppState'
import Navbar from './components/Navbar.vue'
import AppTimer from './components/AppTimer.vue'


export default {
    data() {
        return {
            appState: computed(() => AppState),
            timeElapsed: 0,
            timerInterval: undefined,
            timeLimit: 10,
            thresholds: [
                {
                    color: 'blue',
                    threshold: 1,
                },
                {
                    color: 'orange',
                    threshold: 0.5,
                },
                {
                    color: 'red',
                    threshold: 0.2,
                },
            ],
        };
    },
    methods: {
        startTimer() {
            this.timerInterval = setInterval(() => {
                //Stop counting when there is no more time left
                if (++this.timeElapsed === this.timeLimit) {
                    clearInterval(this.timerInterval);
                }
            }, 1000);
        },
    },
    computed: {
        timeLeft() {
            return this.timeLimit - this.timeElapsed;
        },
        timeFraction() {
            return this.timeLeft / this.timeLimit;
        },
        backgroundHeight() {
            const timeFraction = this.timeFraction;
            //Adjust time fraction the prevent lag when the the left is 0, like we did for the time left progress ring
            const adjTimeFraction = timeFraction - (1 - timeFraction) / this.timeLimit;
            const height = Math.floor(adjTimeFraction * 100);
            return `${height}%`;
        },
        backgroundColor() {
            return this.thresholds.reduce(
                (color, item) =>
                    item.threshold >= this.timeFraction ? item.color : color, undefined
            );
        },
    },
    //Start timer immediately
    mounted() {
        this.startTimer();
    },
    components: { Navbar, AppTimer },
};
</script>
<style lang="scss">
@import "./assets/scss/main.scss";

:root {
    --main-height: calc(100vh - 32px - 64px);
}


footer {
    display: grid;
    place-content: center;
    height: 32px;
}

.background {
    height: 100%;
    position: absolute;
    top: 0;
    width: 100%;
    display: flex;
    flex-direction: column;
    justify-content: end;
    background-color: black;
}

.background .elapsed {
    /*For the height animation */
    transition: all 1s linear;
}
</style>
