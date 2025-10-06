<script>
export default {
    name: 'SceneThree',
    emits: ['goBack', 'goNext'],
    data() {
        return {
            backgroundImage: '/personajes/escenarios/escenario3.png',
            isDialogActive: false,
            currentDialogIndex: 0,
            showDoorButton: false,
            dialogs: [
                { character: 'Alfred Russel', text: 'Ayana, would you like to see something interesting? Choose one of the three doors.' },
                { character: 'Ayara', text: 'Yes, mmm... I choose the middle door.' },
            ]
        }
    },
    mounted() {
        setTimeout(() => {
            this.isDialogActive = true;
        }, 500);
    },
    methods: {
        handleGoBack() {
            this.$emit('goBack');
        },
        handleGoNext() {
            this.$emit('goNext');
        },
        nextDialog() {
            if (this.currentDialogIndex < this.dialogs.length - 1) {
                this.currentDialogIndex++;
            } else {
                this.isDialogActive = false;
                setTimeout(() => {
                    this.showDoorButton = true;
                }, 300);
            }
        }
    }
}
</script>

<template>
    <div class="scene-three-container" :style="{ backgroundImage: `url(${backgroundImage})` }">

        <div v-if="isDialogActive" class="dialog-box" @click="nextDialog">
            <div class="dialog-character">
                {{ dialogs[currentDialogIndex].character }}
            </div>
            <div class="dialog-text">
                {{ dialogs[currentDialogIndex].text }}
            </div>
            <div class="dialog-continue">Click to continue...</div>
        </div>

        <button v-if="showDoorButton" class="door-button" @click="handleGoNext">
            <div class="door-icon">🚪</div>
            <div class="door-text">Click the door</div>
            <div class="door-arrow">↓</div>
        </button>

        <button class="back-button" @click="handleGoBack">
            ← Back
        </button>
    </div>
</template>

<style scoped>
.scene-three-container {
    width: 100%;
    height: 100vh;
    height: 100dvh;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    background-size: cover;
    background-position: center bottom;
    background-repeat: no-repeat;
    position: absolute;
    top: 0;
    left: 0;
    overflow: hidden;
}

.dialog-box {
    position: absolute;
    bottom: 100px;
    left: 50%;
    transform: translateX(-50%);
    background: rgba(0, 0, 0, 0.85);
    border: 3px solid #fff;
    border-radius: 15px;
    padding: 20px 30px;
    max-width: 600px;
    width: 80%;
    z-index: 25;
    cursor: pointer;
    box-shadow: 0 5px 20px rgba(0, 0, 0, 0.5);
    animation: fadeInUp 0.5s ease-out;
}

.dialog-character {
    color: #4a90e2;
    font-size: 16px;
    font-weight: bold;
    margin-bottom: 10px;
    text-transform: uppercase;
}

.dialog-text {
    color: white;
    font-size: 18px;
    line-height: 1.5;
    margin-bottom: 10px;
}

.dialog-continue {
    color: #aaa;
    font-size: 12px;
    text-align: right;
    font-style: italic;
}

/* Botón de la puerta del medio */
.door-button {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 180px;
    height: 280px;
    background: rgba(255, 255, 255, 0.15);
    border: 5px solid rgba(255, 255, 255, 0.8);
    border-radius: 20px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    cursor: pointer;
    backdrop-filter: blur(10px);
    z-index: 100;
    transition: all 0.3s ease;
    animation: doorPulse 2s ease-in-out infinite;
}

.door-button:hover {
    background: rgba(39, 174, 96, 0.3);
    border-color: #27ae60;
    transform: translate(-50%, -50%) scale(1.05);
    box-shadow: 0 0 30px rgba(39, 174, 96, 0.6);
}

.door-icon {
    font-size: 80px;
    margin-bottom: 10px;
    filter: drop-shadow(0 0 10px rgba(255, 255, 255, 0.8));
}

.door-text {
    font-size: 24px;
    font-weight: 900;
    color: white;
    text-shadow: 0 2px 10px rgba(0, 0, 0, 0.8);
    text-transform: uppercase;
    letter-spacing: 2px;
    margin-bottom: 10px;
}

.door-arrow {
    font-size: 40px;
    color: white;
    animation: bounceArrow 1.5s ease-in-out infinite;
    filter: drop-shadow(0 0 10px rgba(255, 255, 255, 0.8));
}

@keyframes doorPulse {

    0%,
    100% {
        box-shadow: 0 0 20px rgba(255, 255, 255, 0.4);
    }

    50% {
        box-shadow: 0 0 40px rgba(39, 174, 96, 0.6);
    }
}

@keyframes bounceArrow {

    0%,
    100% {
        transform: translateY(0);
    }

    50% {
        transform: translateY(10px);
    }
}

.back-button {
    position: absolute;
    bottom: 50px;
    left: 40px;
    padding: 14px 28px;
    background: rgba(231, 76, 60, 0.95);
    color: #ffffff;
    border: 3px solid #c0392b;
    border-radius: 12px;
    font-size: 20px;
    font-weight: 900;
    cursor: pointer;
    transition: all 0.3s ease;
    backdrop-filter: blur(5px);
    z-index: 100;
    box-shadow: 0 6px 20px rgba(231, 76, 60, 0.4);
}

.back-button:hover {
    background: rgba(231, 76, 60, 1);
    transform: scale(1.1);
    box-shadow: 0 4px 15px rgba(231, 76, 60, 0.5);
}

.back-button:active {
    transform: scale(0.98);
}

@keyframes fadeInUp {
    from {
        opacity: 0;
        transform: translateX(-50%) translateY(20px);
    }

    to {
        opacity: 1;
        transform: translateX(-50%) translateY(0);
    }
}

@media (max-width: 768px) {
    .dialog-box {
        bottom: 60px;
        padding: 15px 20px;
        width: 85%;
    }

    .dialog-text {
        font-size: 14px;
    }

    .door-button {
        width: 140px;
        height: 220px;
    }

    .door-icon {
        font-size: 60px;
    }

    .door-text {
        font-size: 18px;
    }

    .door-arrow {
        font-size: 30px;
    }

    .back-button {
        bottom: 20px;
        left: 20px;
        font-size: 16px;
        padding: 10px 20px;
    }
}

@media (max-width: 480px) {
    .dialog-box {
        bottom: 70px;
        padding: 12px 16px;
    }

    .dialog-character {
        font-size: 14px;
    }

    .dialog-text {
        font-size: 13px;
    }

    .door-button {
        width: 120px;
        height: 200px;
    }

    .door-icon {
        font-size: 50px;
    }

    .door-text {
        font-size: 16px;
    }
}
</style>