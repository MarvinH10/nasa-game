<script>
import Scene2 from './Scene2.vue'
import Scene3 from './Scene3.vue'
import Scene4 from './Scene4.vue'

export default {
    name: 'SceneOne',
    components: {
        Scene2,
        Scene3,
        Scene4
    },
    data() {
        return {
            backgroundImage: '/personajes/escenarios/escenario1.1.jpg',
            nativa: {
                x: 100,
                y: 40,
                speed: 4,
                width: 0,
                height: 0,
                facingRight: true,
                isMoving: false
            },
            osito: {
                x: 0,
                y: 10,
                width: 0,
                height: 0,
                visible: false,
                isAnimating: false,
                targetX: 0
            },
            arbusto: {
                x: 0,
                y: 0,
                width: 0
            },
            keys: {
                left: false,
                right: false
            },
            showRotateMessage: false,
            showInteractButton: false,
            isDialogActive: false,
            isAutoMoving: false,
            showScene2: false,
            showScene3: false,
            showScene4: false,
            currentDialogIndex: 0,
            dialogs: [
                { character: 'Bear', text: 'Hello, Are you lost?' },
                { character: 'Ayara', text: 'Yes!, I am very afraid.' },
                { character: 'Ayara', text: 'Could you help me, please?' },
                { character: 'Bear', text: 'Yes, of course!' },
                { character: 'Bear', text: 'I am going to introduce you to my friend the scientist, follow me!' },
            ]
        }
    },
    mounted() {
        this.updateCharacterSize();
        this.checkOrientation();
        this.updateArbustoPosition();

        window.addEventListener('keydown', this.handleKeyDown);
        window.addEventListener('keyup', this.handleKeyUp);
        window.addEventListener('resize', this.handleResize);
        window.addEventListener('orientationchange', this.handleOrientationChange);

        this.gameLoop();
    },
    beforeUnmount() {
        window.removeEventListener('keydown', this.handleKeyDown);
        window.removeEventListener('keyup', this.handleKeyUp);
        window.removeEventListener('resize', this.handleResize);
        window.removeEventListener('orientationchange', this.handleOrientationChange);

        if (this.animationFrame) {
            cancelAnimationFrame(this.animationFrame);
        }
    },
    methods: {
        updateCharacterSize(preservePosition = false) {
            let relativeX = 0;
            if (preservePosition && this.nativa.width > 0) {
                relativeX = this.nativa.x / (window.innerWidth - this.nativa.width);
            }

            const vh = window.innerHeight;
            const vw = window.innerWidth;

            if (vw < 768) {
                this.nativa.height = vh * 0.55;
                this.nativa.speed = 4;
            } else {
                this.nativa.height = vh * 0.30;
                this.nativa.speed = 4;
            }

            this.nativa.width = this.nativa.height * 0.95;
            this.osito.width = this.nativa.width;
            this.osito.height = this.nativa.height;

            if (preservePosition) {
                this.nativa.x = relativeX * (window.innerWidth - this.nativa.width);
                this.nativa.x = Math.max(0, Math.min(this.nativa.x, window.innerWidth - this.nativa.width));
            }
        },
        updateArbustoPosition() {
            const vw = window.innerWidth;
            let arbustoWidth;

            if (vw < 768) {
                arbustoWidth = vw * 0.18;
                arbustoWidth = Math.max(80, arbustoWidth);
            } else {
                arbustoWidth = vw * 0.15;
                arbustoWidth = Math.max(100, Math.min(200, arbustoWidth));
            }

            this.arbusto.width = arbustoWidth;
            this.arbusto.x = vw - (vw * 0.06) - arbustoWidth;

            this.osito.x = this.arbusto.x + 10;
            this.osito.targetX = this.arbusto.x - this.osito.width + 40;
        },
        checkOrientation() {
            const isMobile = window.innerWidth < 768;
            const isPortrait = window.innerHeight > window.innerWidth;
            this.showRotateMessage = isMobile && isPortrait;
        },
        handleResize() {
            this.updateCharacterSize(true);
            this.updateArbustoPosition();
            this.checkOrientation();
        },
        handleOrientationChange() {
            setTimeout(() => {
                this.updateCharacterSize(true);
                this.updateArbustoPosition();
                this.checkOrientation();
            }, 100);
        },
        handleKeyDown(e) {
            if (e.key === 'ArrowLeft') {
                this.keys.left = true;
                this.nativa.facingRight = false;
                this.nativa.isMoving = true;
            }
            if (e.key === 'ArrowRight') {
                this.keys.right = true;
                this.nativa.facingRight = true;
                this.nativa.isMoving = true;
            }
        },
        handleKeyUp(e) {
            if (e.key === 'ArrowLeft') {
                this.keys.left = false;
            }
            if (e.key === 'ArrowRight') {
                this.keys.right = false;
            }

            if (!this.keys.left && !this.keys.right) {
                this.nativa.isMoving = false;
            }
        },
        checkProximityToArbusto() {
            const distance = Math.abs(this.nativa.x - this.arbusto.x);
            const proximityThreshold = 380;
            const minDistance = 350;

            if (distance < proximityThreshold && distance >= minDistance && !this.osito.visible) {
                this.showInteractButton = true;
            } else {
                this.showInteractButton = false;
            }
        },
        handleInteract() {
            this.osito.visible = true;
            this.osito.isAnimating = true;
            this.showInteractButton = false;

            setTimeout(() => {
                this.osito.isAnimating = false;
                this.isDialogActive = true;
                this.currentDialogIndex = 0;
            }, 800);
        },
        nextDialog() {
            if (this.currentDialogIndex < this.dialogs.length - 1) {
                this.currentDialogIndex++;
            } else {
                this.isDialogActive = false;
                this.isAutoMoving = true;
            }
        },
        animateOsito() {
            if (this.osito.isAnimating) {
                const diff = this.osito.targetX - this.osito.x;
                if (Math.abs(diff) > 2) {
                    this.osito.x += diff * 0.1;
                } else {
                    this.osito.x = this.osito.targetX;
                }
            }
        },
        gameLoop() {
            if (this.isAutoMoving) {
                const autoSpeed = 2;
                this.nativa.x += autoSpeed;
                this.osito.x += autoSpeed;

                const maxX = window.innerWidth - this.nativa.width;
                if (this.nativa.x >= maxX) {
                    this.nativa.x = maxX;
                    this.osito.x = maxX - this.nativa.width - 40;
                    this.showScene2 = true;
                    this.isAutoMoving = false;
                    return;
                }
            }
            else if (this.keys.left && !this.isDialogActive) {
                this.nativa.x -= this.nativa.speed;
                if (this.nativa.x < 0) {
                    this.nativa.x = 0;
                }
            }
            else if (this.keys.right && !this.isDialogActive) {
                this.nativa.x += this.nativa.speed;
                const maxX = window.innerWidth - this.nativa.width;

                if (this.showInteractButton) {
                    const colliderX = this.arbusto.x - 350;
                    if (this.nativa.x > colliderX) {
                        this.nativa.x = colliderX;
                    }
                } else if (this.nativa.x > maxX) {
                    this.nativa.x = maxX;
                }
            }

            this.checkProximityToArbusto();
            this.animateOsito();

            this.animationFrame = requestAnimationFrame(this.gameLoop);
        },
        resetScene() {
            if (this.animationFrame) {
                cancelAnimationFrame(this.animationFrame);
            }

            this.nativa.x = 100;
            this.nativa.y = 40;
            this.nativa.facingRight = true;
            this.nativa.isMoving = false;

            this.osito.visible = false;
            this.osito.isAnimating = false;

            this.keys.left = false;
            this.keys.right = false;

            this.showInteractButton = false;
            this.isDialogActive = false;
            this.isAutoMoving = false;
            this.showScene2 = false;
            this.currentDialogIndex = 0;

            this.updateCharacterSize();
            this.updateArbustoPosition();

            this.$nextTick(() => {
                this.gameLoop();
            });
        }
    }
}
</script>

<template>
    <div v-if="!showScene2 && !showScene3 && !showScene4">
        <div class="scene-container" :style="{ backgroundImage: `url(${backgroundImage})` }">
            <div v-if="showRotateMessage" class="rotate-message">
                <div class="rotate-content">
                    <svg width="80" height="80" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                        <rect x="2" y="3" width="20" height="14" rx="2" />
                        <path d="M8 21h8" />
                        <path d="M12 17v4" />
                    </svg>
                    <p>Por favor, gira tu dispositivo</p>
                    <p class="subtitle">para una mejor experiencia</p>
                </div>
            </div>

            <div v-if="osito.visible" class="character character-osito" :style="{
                left: osito.x + 'px',
                bottom: osito.y + 'px',
                width: osito.width + 'px',
                height: osito.height + 'px',
                transform: 'scaleX(-1)'
            }">
                <img src="/personajes/oso/osito.png" alt="Bear" draggable="false" />
            </div>

            <div class="arbusto">
                <img src="/personajes/escenarios/arbusto.png" alt="Arbusto" draggable="false" />
            </div>

            <div class="character" :style="{
                left: nativa.x + 'px',
                bottom: nativa.y + 'px',
                width: nativa.width + 'px',
                height: nativa.height + 'px',
                transform: nativa.facingRight ? 'scaleX(1)' : 'scaleX(-1)'
            }">
                <img src="/personajes/nativa/nativa_parada.png" alt="Ayana" draggable="false" />
            </div>

            <button v-if="showInteractButton" class="interact-button" @click="handleInteract">
                Interact
            </button>

            <div v-if="isDialogActive" class="dialog-box" @click="nextDialog">
                <div class="dialog-character">
                    {{ dialogs[currentDialogIndex].character }}
                </div>
                <div class="dialog-text">
                    {{ dialogs[currentDialogIndex].text }}
                </div>
                <div class="dialog-continue">Click to continue...</div>
            </div>

            <div class="scene-content">
                <div class="instructions">
                    <p>← → Use the arrows to move the native Ayara towards the bush</p>
                </div>
            </div>
        </div>
    </div>

    <Scene2 v-else-if="showScene2 && !showScene3 && !showScene4" @goBack="resetScene" @goNext="showScene3 = true" />

    <Scene3 v-else-if="showScene3 && !showScene4" @goBack="showScene3 = false" @goNext="showScene4 = true" />

    <Scene4 v-else-if="showScene4" @goBack="showScene4 = false" />
</template>

<style scoped>
.scene-container {
    width: 100%;
    height: 100%;
    height: 100dvh;
    position: absolute;
    background-size: cover;
    background-position: center bottom;
    background-repeat: no-repeat;
    overflow: hidden;
}

.character {
    position: absolute;
    transition: transform 0.1s ease;
    z-index: 10;
    image-rendering: crisp-edges;
    will-change: left, transform;
}

.character-osito {
    z-index: 12;
}

.character img {
    width: 100%;
    height: 100%;
    object-fit: contain;
    pointer-events: none;
}

.arbusto {
    position: absolute;
    right: 6%;
    bottom: -35px;
    width: 15vw;
    max-width: 200px;
    min-width: 100px;
    height: auto;
    z-index: 8;
    image-rendering: crisp-edges;
}

.arbusto img {
    width: 140%;
    height: 140%;
    object-fit: contain;
    pointer-events: none;
}

.interact-button {
    position: absolute;
    bottom: 120px;
    left: 50%;
    transform: translateX(-50%);
    background: green;
    border: 3px solid white;
    border-radius: 12px;
    padding: 15px 30px;
    font-size: 20px;
    font-weight: bold;
    color: white;
    cursor: pointer;
    z-index: 15;
    transition: all 0.3s ease;
    box-shadow: 0 8px 25px rgba(0, 0, 0, 0.5);
    animation: bounce 1s ease-in-out infinite;
}

.interact-button:hover {
    background: white;
    border: 3px solid green;
    color: green;
    box-shadow: 0 8px 25px rgba(0, 0, 0, 0.5);
    transform: translateX(-50%) scale(1.1);
}

@keyframes bounce {

    0%,
    100% {
        transform: translateX(-50%) translateY(0);
    }

    50% {
        transform: translateX(-50%) translateY(-10px);
    }
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

.scene-content {
    position: relative;
    z-index: 2;
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
    padding-top: 20px;
    pointer-events: none;
}

.instructions {
    background: rgba(0, 0, 0, 0.7);
    padding: 15px 30px;
    border-radius: 10px;
    color: white;
    font-size: 18px;
    text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8);
    max-width: 90%;
}

.rotate-message {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.95);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 9999;
}

.rotate-content {
    text-align: center;
    color: white;
    animation: pulse 2s ease-in-out infinite;
}

.rotate-content svg {
    margin-bottom: 20px;
    animation: rotate 2s ease-in-out infinite;
}

.rotate-content p {
    font-size: 24px;
    font-weight: bold;
    margin: 10px 0;
}

.rotate-content .subtitle {
    font-size: 16px;
    font-weight: normal;
    opacity: 0.8;
}

@keyframes rotate {

    0%,
    100% {
        transform: rotate(0deg);
    }

    50% {
        transform: rotate(90deg);
    }
}

@keyframes pulse {

    0%,
    100% {
        opacity: 1;
    }

    50% {
        opacity: 0.7;
    }
}

@media (max-width: 768px) and (orientation: landscape) {
    .instructions {
        font-size: 12px;
        padding: 8px 16px;
    }

    .scene-content {
        padding-top: 10px;
    }

    .arbusto {
        width: 18vw;
        min-width: 80px;
    }

    .interact-button {
        font-size: 16px;
        padding: 10px 20px;
        bottom: 80px;
    }

    .dialog-box {
        bottom: 60px;
        padding: 15px 20px;
    }

    .dialog-text {
        font-size: 14px;
    }
}

@media (min-width: 769px) {
    .instructions {
        font-size: 20px;
    }
}

@media (max-width: 768px) and (orientation: portrait) {
    .scene-container {
        height: 100dvh;
    }
}
</style>