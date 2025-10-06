<script>
export default {
    name: 'GamePlants',
    emits: ['gameComplete', 'goBack'],
    data() {
        return {
            backgroundImage: '/personajes/escenarios/tierra-fondo.jpg',
            holes: [
                { id: 1, x: 15, y: 60, planted: false, watered: false, plantType: null },
                { id: 2, x: 35, y: 65, planted: false, watered: false, plantType: null },
                { id: 3, x: 55, y: 60, planted: false, watered: false, plantType: null },
                { id: 4, x: 75, y: 65, planted: false, watered: false, plantType: null }
            ],
            availablePlants: [
                { id: 'tree', name: 'Tree', image: '/personajes/plantas/semilla-arbol.png' },
                { id: 'flower', name: 'Flower', image: '/personajes/plantas/semilla-flor.png' },
                { id: 'bush', name: 'Bush', image: '/personajes/plantas/semilla-arbusto.png' }
            ],
            draggedPlant: null,
            isDraggingWater: false,
            timeProgress: 0,
            maxTime: 100,
            isGrowing: false,
            showMessage: false,
            showReadyMessage: false,
            showTimeline: false,
            allPlanted: false
        }
    },
    computed: {
        allHolesPlanted() {
            return this.holes.every(hole => hole.planted);
        },
        allHolesWatered() {
            return this.holes.every(hole => hole.watered);
        },
        currentStage() {
            if (this.timeProgress >= 100) return 'full';
            if (this.timeProgress >= 50) return 'young';
            if (this.timeProgress >= 25) return 'sprout';
            return 'seed';
        }
    },
    watch: {
        allHolesWatered(newVal) {
            if (newVal && !this.showReadyMessage) {
                this.showReadyMessage = true;
            }
        },
        timeProgress(newVal) {
            this.updateVideoProgress(newVal);
        }
    },
    methods: {
        handleDragStart(plant) {
            this.draggedPlant = plant;
            this.isDraggingWater = false;
        },
        handleWaterDragStart() {
            this.isDraggingWater = true;
            this.draggedPlant = null;
        },
        handleDragOver(event) {
            event.preventDefault();
        },
        handleDrop(hole) {
            if (this.draggedPlant && !hole.planted) {
                hole.planted = true;
                hole.plantType = this.draggedPlant.id;
                this.draggedPlant = null;

                if (this.allHolesPlanted && !this.allPlanted) {
                    this.allPlanted = true;
                }
            } else if (this.isDraggingWater && hole.planted && !hole.watered) {
                hole.watered = true;
                this.isDraggingWater = false;
            }
        },
        handleTimeChange(event) {
            this.timeProgress = parseInt(event.target.value);
        },
        playGrowth() {
            if (this.isGrowing) return;

            this.isGrowing = true;
            const interval = setInterval(() => {
                if (this.timeProgress < this.maxTime) {
                    this.timeProgress += 1;
                } else {
                    clearInterval(interval);
                    this.isGrowing = false;
                    this.showMessage = true;
                }
            }, 100);
        },
        resetTime() {
            this.timeProgress = 0;
        },
        getPlantVideo(hole) {
            if (!hole.planted) return '';
            return `/videos/plantas/${hole.plantType}.mp4`;
        },
        updateVideoProgress(progress) {
            this.$nextTick(() => {
                const videos = document.querySelectorAll('.plant-video');
                videos.forEach(video => {
                    if (video.duration) {
                        const targetTime = (progress / 100) * video.duration;
                        video.currentTime = targetTime;
                    }
                });
            });
        },
        handleVideoLoaded(event) {
            const video = event.target;
            if (video.duration) {
                const targetTime = (this.timeProgress / 100) * video.duration;
                video.currentTime = targetTime;
            }
        },
        handleContinue() {
            this.$emit('gameComplete');
        },
        handleGoBack() {
            this.$emit('goBack');
        },
        startTimeline() {
            this.showReadyMessage = false;
            this.showTimeline = true;
        }
    }
}
</script>

<template>
    <div class="game-container" :style="{ backgroundImage: `url(${backgroundImage})` }">

        <button class="back-button" @click="handleGoBack">
            ← Exit
        </button>

        <div v-if="showMessage" class="final-message">
            <div class="message-box">
                <h2>Congratulations!</h2>
                <p class="reflection">
                    Plants are essential for life on Earth.
                    They produce the oxygen we breathe, purify the air,
                    prevent soil erosion, and are home to thousands of species.
                </p>
                <p class="reflection">
                    Every tree you plant helps combat climate change
                    and protects our planet for future generations.
                </p>
                <button class="continue-button" @click="handleContinue">
                    Continue →
                </button>
            </div>
        </div>

        <div v-if="showReadyMessage" class="ready-message">
            <div class="ready-box">
                <h2>Excellent work!</h2>
                <p>You have planted and watered all the seeds.</p>
                <p class="highlight">Are you ready to see the plants' progress?</p>
                <button class="ready-button" @click="startTimeline">
                    Yes, see the progress!
                </button>
            </div>
        </div>

        <div v-if="!allPlanted" class="plants-panel">
            <h3>Drag the plants</h3>
            <div class="plant-list">
                <div v-for="plant in availablePlants" :key="plant.id" class="plant-item" draggable="true"
                    @dragstart="handleDragStart(plant)">
                    <img :src="plant.image" :alt="plant.name" draggable="false">
                    <span>{{ plant.name }}</span>
                </div>
            </div>
        </div>

        <div v-if="allPlanted && !allHolesWatered" class="water-panel">
            <h3>Water the plants</h3>
            <div class="watering-can" draggable="true" @dragstart="handleWaterDragStart">
                <img src="/personajes/plantas/regadera.png" alt="Watering Can" draggable="false">
                <span>Watering Can</span>
            </div>
        </div>

        <div class="ground-area">
            <div v-for="hole in holes" :key="hole.id" class="hole"
                :class="{ 'hole-filled': hole.planted, 'hole-watered': hole.watered }"
                :style="{ left: hole.x + '%', top: hole.y + '%' }" @dragover="handleDragOver" @drop="handleDrop(hole)">

                <div v-if="!hole.planted" class="hole-empty"></div>

                <div v-else class="planted-area">
                    <video v-if="showTimeline" :src="getPlantVideo(hole)" class="plant-video" muted
                        @loadedmetadata="handleVideoLoaded"></video>

                    <img v-else src="/personajes/plantas/semilla-planted.png" alt="Planted seed" class="plant-image">

                    <div v-if="hole.watered" class="water-indicator">💧</div>
                    <div v-else class="needs-water">Needs water!</div>
                </div>
            </div>
        </div>

        <div v-if="showTimeline" class="timeline-container">
            <h3>Growth Timeline</h3>
            <div class="timeline-controls">
                <button @click="resetTime" class="time-button">⏮ Start</button>
                <button @click="playGrowth" :disabled="isGrowing" class="time-button play-button">
                    {{ isGrowing ? '⏸ Growing...' : '▶ Play' }}
                </button>
            </div>
            <div class="timeline-slider">
                <input type="range" min="0" :max="maxTime" v-model="timeProgress" @input="handleTimeChange"
                    class="slider">
                <div class="timeline-labels">
                    <span>Seed</span>
                    <span>Sprout</span>
                    <span>Young</span>
                    <span>Complete</span>
                </div>
            </div>
            <div class="progress-info">
                Progress: {{ timeProgress }}% - Stage: {{ currentStage }}
            </div>
        </div>

        <div v-if="!allPlanted" class="instructions">
            <p>Step 1: Drag the plants to the holes</p>
            <p>Planted: {{holes.filter(h => h.planted).length}} / {{ holes.length }}</p>
        </div>

        <div v-else-if="allPlanted && !allHolesWatered" class="instructions">
            <p>Step 2: Water all the plants</p>
            <p>Watered: {{holes.filter(h => h.watered).length}} / {{ holes.length }}</p>
        </div>
    </div>
</template>

<style scoped>
.plant-video {
    width: 100%;
    height: 100%;
    object-fit: cover;
    object-position: center;
    pointer-events: none;
    border-radius: 10px;
}

.game-container {
    width: 100%;
    height: 100vh;
    height: 100dvh;
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
    position: relative;
    overflow: hidden;
}

.back-button {
    position: absolute;
    bottom: 20px;
    left: 20px;
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
    z-index: 1000;
    box-shadow: 0 6px 20px rgba(231, 76, 60, 0.4);
}

.back-button:hover {
    background: rgba(231, 76, 60, 1);
    transform: scale(1.1);
    box-shadow: 0 8px 25px rgba(231, 76, 60, 0.6);
}

.back-button:active {
    transform: scale(0.98);
}

.plants-panel,
.water-panel {
    position: absolute;
    top: 20px;
    right: 20px;
    background: rgba(255, 255, 255, 0.95);
    padding: 20px;
    border-radius: 15px;
    box-shadow: 0 5px 20px rgba(0, 0, 0, 0.3);
    z-index: 10;
    max-width: 200px;
}

.plants-panel h3,
.water-panel h3 {
    margin: 0 0 15px 0;
    color: #2c5f2d;
    font-size: 18px;
}

.plant-list {
    display: flex;
    flex-direction: column;
    gap: 10px;
}

.plant-item,
.watering-can {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 10px;
    background: #f0f8f0;
    border-radius: 10px;
    cursor: grab;
    transition: all 0.3s ease;
    border: 2px solid #4a7c59;
}

.watering-can {
    border: 3px solid #3498db;
    background: #e3f2fd;
    animation: waterShine 2s ease-in-out infinite;
}

@keyframes waterShine {

    0%,
    100% {
        box-shadow: 0 0 10px rgba(52, 152, 219, 0.3);
    }

    50% {
        box-shadow: 0 0 20px rgba(52, 152, 219, 0.6);
    }
}

.plant-item:hover,
.watering-can:hover {
    transform: scale(1.05);
    box-shadow: 0 3px 10px rgba(74, 124, 89, 0.4);
}

.plant-item:active,
.watering-can:active {
    cursor: grabbing;
}

.plant-item img,
.watering-can img {
    width: 40px;
    height: 40px;
    object-fit: contain;
}

.plant-item span,
.watering-can span {
    font-size: 14px;
    font-weight: bold;
    color: #2c5f2d;
}

.ground-area {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
}

.hole {
    position: absolute;
    width: 120px;
    height: 120px;
    transition: all 0.3s ease;
}

.hole-empty {
    width: 100%;
    height: 100%;
    background: radial-gradient(circle, #4a3520 0%, #6b4423 50%, transparent 70%);
    border-radius: 50%;
    border: 3px dashed #8b5a2b;
    animation: pulse 2s ease-in-out infinite;
}

@keyframes pulse {

    0%,
    100% {
        transform: scale(1);
        opacity: 0.7;
    }

    50% {
        transform: scale(1.05);
        opacity: 1;
    }
}

.hole-watered .hole-empty {
    background: #4a3520;
    border: none;
    animation: none;
}

.planted-area {
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    position: relative;
}

.plant-image {
    max-width: 100%;
    max-height: 100%;
    object-fit: contain;
    animation: grow 0.5s ease-out;
}

.water-indicator {
    position: absolute;
    top: -10px;
    right: -10px;
    font-size: 24px;
    animation: waterDrop 1s ease-out;
}

@keyframes waterDrop {
    0% {
        transform: translateY(-20px);
        opacity: 0;
    }

    100% {
        transform: translateY(0);
        opacity: 1;
    }
}

.needs-water {
    position: absolute;
    bottom: -30px;
    font-size: 12px;
    font-weight: bold;
    color: #e74c3c;
    background: rgba(255, 255, 255, 0.9);
    padding: 5px 10px;
    border-radius: 5px;
    animation: blink 1.5s ease-in-out infinite;
}

@keyframes blink {

    0%,
    100% {
        opacity: 1;
    }

    50% {
        opacity: 0.5;
    }
}

@keyframes grow {
    from {
        transform: scale(0);
        opacity: 0;
    }

    to {
        transform: scale(1);
        opacity: 1;
    }
}

.ready-message {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    height: 100dvh;
    background: rgba(0, 0, 0, 0.85);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 1000;
    animation: fadeIn 0.5s ease-out;
}

.ready-box {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    padding: 40px;
    border-radius: 20px;
    max-width: 500px;
    text-align: center;
    animation: scaleIn 0.5s ease-out;
    color: white;
}

.ready-box h2 {
    font-size: 32px;
    margin-bottom: 20px;
}

.ready-box p {
    font-size: 18px;
    margin: 10px 0;
}

.highlight {
    font-weight: bold;
    font-size: 20px !important;
    margin: 20px 0 !important;
}

.ready-button {
    margin-top: 20px;
    padding: 15px 40px;
    background: #27ae60;
    color: white;
    border: none;
    border-radius: 12px;
    font-size: 20px;
    font-weight: bold;
    cursor: pointer;
    transition: all 0.3s ease;
}

.ready-button:hover {
    background: #2ecc71;
    transform: scale(1.1);
}

.timeline-container {
    position: absolute;
    top: 20px;
    left: 50%;
    transform: translateX(-50%);
    background: rgba(255, 255, 255, 0.95);
    padding: 20px 30px;
    border-radius: 15px;
    box-shadow: 0 5px 20px rgba(0, 0, 0, 0.3);
    min-width: 600px;
    z-index: 15;
}

.timeline-container h3 {
    margin: 0 0 15px 0;
    color: #2c5f2d;
    text-align: center;
}

.timeline-controls {
    display: flex;
    gap: 10px;
    justify-content: center;
    margin-bottom: 15px;
}

.time-button {
    padding: 10px 20px;
    background: #4a7c59;
    color: white;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    font-weight: bold;
    transition: all 0.3s ease;
}

.time-button:hover:not(:disabled) {
    background: #2c5f2d;
    transform: scale(1.05);
}

.time-button:disabled {
    opacity: 0.6;
    cursor: not-allowed;
}

.play-button {
    background: #27ae60;
}

.timeline-slider {
    margin: 15px 0;
}

.slider {
    width: 100%;
    height: 8px;
    border-radius: 5px;
    background: #d3d3d3;
    outline: none;
    cursor: pointer;
}

.slider::-webkit-slider-thumb {
    appearance: none;
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background: #27ae60;
    cursor: pointer;
}

.slider::-moz-range-thumb {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background: #27ae60;
    cursor: pointer;
    border: none;
}

.timeline-labels {
    display: flex;
    justify-content: space-between;
    margin-top: 10px;
    font-size: 12px;
    color: #666;
}

.progress-info {
    text-align: center;
    margin-top: 10px;
    font-weight: bold;
    color: #2c5f2d;
}

.instructions {
    position: absolute;
    top: 20px;
    left: 20px;
    background: rgba(39, 174, 96, 0.95);
    color: white;
    padding: 15px 25px;
    border-radius: 12px;
    font-size: 18px;
    font-weight: bold;
    z-index: 10;
}

.instructions p {
    margin: 5px 0;
}

.final-message {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    height: 100dvh;
    background: rgba(0, 0, 0, 0.8);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 1000;
    animation: fadeIn 0.5s ease-out;
}

@keyframes fadeIn {
    from {
        opacity: 0;
    }

    to {
        opacity: 1;
    }
}

.message-box {
    background: white;
    padding: 40px;
    border-radius: 20px;
    max-width: 600px;
    text-align: center;
    animation: scaleIn 0.5s ease-out;
}

@keyframes scaleIn {
    from {
        transform: scale(0.8);
        opacity: 0;
    }

    to {
        transform: scale(1);
        opacity: 1;
    }
}

.message-box h2 {
    color: #27ae60;
    font-size: 36px;
    margin-bottom: 20px;
}

.reflection {
    font-size: 18px;
    line-height: 1.6;
    color: #333;
    margin-bottom: 15px;
    text-align: left;
}

.continue-button {
    margin-top: 20px;
    padding: 15px 40px;
    background: #27ae60;
    color: white;
    border: none;
    border-radius: 12px;
    font-size: 20px;
    font-weight: bold;
    cursor: pointer;
    transition: all 0.3s ease;
}

.continue-button:hover {
    background: #2c5f2d;
    transform: scale(1.1);
}

@media (max-width: 768px) {

    .plants-panel,
    .water-panel {
        top: 10px;
        right: 10px;
        max-width: 150px;
        padding: 15px;
    }

    .timeline-container {
        min-width: 90%;
        padding: 15px;
    }

    .hole {
        width: 80px;
        height: 80px;
    }

    .message-box,
    .ready-box {
        padding: 30px 20px;
        margin: 20px;
    }

    .back-button {
        bottom: 15px;
        left: 15px;
        padding: 12px 24px;
        font-size: 18px;
    }
}
</style>