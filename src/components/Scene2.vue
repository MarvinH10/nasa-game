<script>
export default {
    name: 'SceneTwo',
    emits: ['goBack', 'goNext'],
    data() {
        return {
            backgroundImage: '/personajes/escenarios/escenario2.jpg',
            comicPanels: [
                { image: '/personajes/escenarios/escenario2.1.png', visible: false },
                { image: '/personajes/escenarios/escenario2.2.png', visible: false },
                { image: '/personajes/escenarios/escenario2.3.png', visible: false },
                { image: '/personajes/escenarios/escenario2.4.png', visible: false },
                { image: '/personajes/escenarios/escenario2.5.png', visible: false },
                { image: '/personajes/escenarios/escenario2.6.png', visible: false },
            ]
        }
    },
    mounted() {
        this.animatePanels();
    },
    methods: {
        handleGoBack() {
            this.$emit('goBack');
        },
        handleGoNext() {
            this.$emit('goNext');
        },
        animatePanels() {
            this.comicPanels.forEach((panel, index) => {
                setTimeout(() => {
                    panel.visible = true;
                }, index * 400);
            });
        }
    }
}
</script>

<template>
    <div class="scene-two-container" :style="{ backgroundImage: `url(${backgroundImage})` }">
        <div class="comic-container">
            <div v-for="(panel, index) in comicPanels" :key="index" class="comic-panel"
                :class="{ 'panel-visible': panel.visible }" :style="{ animationDelay: index * 0.1 + 's' }">
                <div class="panel-content">
                    <img :src="panel.image" :alt="'Panel ' + (index + 1)" />
                </div>
            </div>
        </div>

        <button class="back-button" @click="handleGoBack">
            ← Back
        </button>

        <button class="next-button" @click="handleGoNext">
            Next →
        </button>
    </div>
</template>

<style scoped>
.scene-two-container {
    width: 100%;
    height: 100vh;
    height: 100dvh;
    display: flex;
    justify-content: center;
    align-items: center;
    background-size: cover;
    background-position: center bottom;
    background-repeat: no-repeat;
    position: absolute;
    top: 0;
    left: 0;
    padding: 20px;
    overflow: hidden;
}

.comic-container {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: repeat(2, 1fr);
    gap: 0;
    width: 90%;
    max-width: 1400px;
    height: 80%;
    max-height: 700px;
    background: white;
    border: 8px solid black;
    box-shadow: 0 10px 40px rgba(0, 0, 0, 0.8);
}

.comic-panel {
    position: relative;
    border: 4px solid black;
    background: white;
    overflow: hidden;
    opacity: 0;
    transform: scale(2) translateZ(100px);
    transition: all 0.6s cubic-bezier(0.68, -0.55, 0.265, 1.55);
}

.comic-panel.panel-visible {
    opacity: 1;
    transform: scale(1) translateZ(0);
}

.panel-content {
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    background: #f5f5f5;
}

.panel-content img {
    width: 100%;
    height: 100%;
    object-fit: cover;
}

.back-button {
    position: absolute;
    bottom: 50px;
    left: 40px;
    padding: 12px 24px;
    background: rgba(231, 76, 60, 0.9);
    color: #ffffff;
    border: 3px solid #c0392b;
    border-radius: 10px;
    font-size: 18px;
    font-weight: 900;
    cursor: pointer;
    transition: all 0.3s ease;
    backdrop-filter: blur(5px);
    z-index: 100;
}

.back-button:hover {
    background: rgba(231, 76, 60, 1);
    transform: scale(1.1);
    box-shadow: 0 4px 15px rgba(231, 76, 60, 0.5);
}

.back-button:active {
    transform: scale(0.95);
}

.next-button {
    position: absolute;
    bottom: 50px;
    right: 80px;
    padding: 12px 24px;
    background: rgba(39, 174, 96, 0.9);
    color: #ffffff;
    border: 3px solid #1a7a3e;
    border-radius: 10px;
    font-size: 18px;
    font-weight: 900;
    cursor: pointer;
    transition: all 0.3s ease;
    backdrop-filter: blur(5px);
    z-index: 100;
}

.next-button:hover {
    background: rgba(39, 174, 96, 1);
    transform: scale(1.1);
    box-shadow: 0 4px 15px rgba(39, 174, 96, 0.5);
}

.next-button:active {
    transform: scale(0.95);
}

@media (max-width: 768px) {
    .comic-container {
        width: 95%;
        height: 70%;
        grid-template-columns: repeat(2, 1fr);
        grid-template-rows: repeat(3, 1fr);
    }

    .back-button,
    .next-button {
        bottom: 20px;
        font-size: 16px;
        padding: 10px 20px;
    }

    .back-button {
        left: 20px;
    }

    .next-button {
        right: 20px;
    }
}

@media (max-width: 480px) {
    .comic-container {
        width: 95%;
        height: 85%;
        border: 4px solid black;
    }

    .comic-panel {
        border: 2px solid black;
    }
}
</style>