<script>
export default {
    name: 'SceneFour',
    emits: ['goBack'],
    data() {
        return {
            backgroundImage: '/personajes/escenarios/escenario4.jpg',
            mousePosition: {
                x: 0,
                y: 0
            },
            screenCursor: {
                x: 50,
                y: 50,
                visible: false
            },
            physicalMouse: {
                x: 0,
                y: 0
            },
            screenBounds: {
                left: 0,
                top: 0,
                right: 0,
                bottom: 0
            },
            isOverScreen: false,
            currentVideo: null,
            videos: [
                {
                    id: 1,
                    name: 'Video 1',
                    url: '/videos/video1-completo.mp4',
                    preview: '/videos/fondo1pc.mp4'
                },
                {
                    id: 2,
                    name: 'Video 2',
                    url: '/videos/video2-completo.mp4',
                    preview: '/videos/fondo2pc.mp4'
                },
                {
                    id: 3,
                    name: 'Video 3',
                    url: '/videos/video3-completo.mp4',
                    preview: '/videos/fondo3pc.mp4'
                },
                {
                    id: 4,
                    name: 'Video 4',
                    url: '/videos/video4-completo.mp4',
                    preview: '/videos/fondo4pc.mp4'
                }
            ]
        }
    },
    mounted() {
        window.addEventListener('mousemove', this.handleMouseMove);
        this.$nextTick(() => {
            this.updateScreenBounds();
        });
        window.addEventListener('resize', this.updateScreenBounds);
    },
    beforeUnmount() {
        window.removeEventListener('mousemove', this.handleMouseMove);
        window.removeEventListener('resize', this.updateScreenBounds);
    },
    methods: {
        handleGoBack() {
            this.$emit('goBack');
        },
        updateScreenBounds() {
            const screen = this.$refs.monitorScreen;
            if (screen) {
                const rect = screen.getBoundingClientRect();
                this.screenBounds = {
                    left: rect.left,
                    top: rect.top,
                    right: rect.right,
                    bottom: rect.bottom,
                    width: rect.width,
                    height: rect.height
                };
            }
        },
        handleMouseMove(event) {
            this.mousePosition.x = event.clientX;
            this.mousePosition.y = event.clientY;

            const { left, top, right, bottom, width, height } = this.screenBounds;

            if (event.clientX >= left && event.clientX <= right &&
                event.clientY >= top && event.clientY <= bottom) {

                this.screenCursor.visible = true;
                this.isOverScreen = true;

                const relativeX = ((event.clientX - left) / width) * 100;
                const relativeY = ((event.clientY - top) / height) * 100;

                this.screenCursor.x = Math.max(0, Math.min(100, relativeX));
                this.screenCursor.y = Math.max(0, Math.min(100, relativeY));
            } else {
                this.screenCursor.visible = false;
                this.isOverScreen = false;
            }

            const mouseContainer = this.$refs.mouseContainer;
            if (mouseContainer) {
                const containerRect = mouseContainer.getBoundingClientRect();
                const centerX = containerRect.left + containerRect.width / 2;
                const centerY = containerRect.top + containerRect.height / 2;

                const offsetX = (event.clientX - centerX) * 0.08;
                const offsetY = (event.clientY - centerY) * 0.08;

                this.physicalMouse.x = Math.max(-40, Math.min(40, offsetX));
                this.physicalMouse.y = Math.max(-40, Math.min(40, offsetY));
            }
        },
        openVideo(video) {
            this.currentVideo = video;
        },
        closeVideo() {
            this.currentVideo = null;
        }
    }
}
</script>

<template>
    <div>
        <div v-if="currentVideo" class="video-fullscreen">
            <video class="fullscreen-video" controls autoplay>
                <source :src="currentVideo.url" type="video/mp4">
                Tu navegador no soporta el elemento de video.
            </video>
            <button class="close-video-button" @click="closeVideo">
                ✕ Cerrar
            </button>
        </div>

        <div v-else class="scene-four-container" :class="{ 'hide-cursor': isOverScreen }"
            :style="{ backgroundImage: `url(${backgroundImage})` }">
            <div class="computer-monitor">
                <div class="monitor-frame">
                    <div class="monitor-screen" ref="monitorScreen">
                        <div class="screen-content">
                            <div class="video-grid-container">
                                <div class="video-grid">
                                    <div v-for="video in videos" :key="video.id" class="video-column"
                                        @click="openVideo(video)">
                                        <video class="video-background" autoplay loop muted playsinline>
                                            <source :src="video.preview" type="video/mp4">
                                        </video>

                                        <div class="video-overlay">
                                            <div class="video-icon">▶</div>
                                            <div class="video-name">{{ video.name }}</div>
                                        </div>
                                    </div>
                                </div>
                            </div>

                            <div v-if="screenCursor.visible" class="screen-cursor" :style="{
                                left: screenCursor.x + '%',
                                top: screenCursor.y + '%'
                            }">
                                👆
                            </div>
                        </div>
                    </div>
                    <div class="monitor-bezel-bottom">
                        <div class="monitor-logo"></div>
                    </div>
                </div>
                <div class="monitor-stand">
                    <div class="stand-neck"></div>
                    <div class="stand-base"></div>
                </div>
            </div>

            <div class="mouse-container" ref="mouseContainer" :style="{
                transform: `translateY(-50%) translate(${physicalMouse.x}px, ${physicalMouse.y}px)`
            }">
                <div class="mouse">
                    <div class="mouse-top">
                        <div class="mouse-left-button"></div>
                        <div class="mouse-scroll-area">
                            <div class="mouse-scroll"></div>
                        </div>
                        <div class="mouse-right-button"></div>
                    </div>
                    <div class="mouse-body"></div>
                </div>
            </div>

            <button class="back-button" @click="handleGoBack">
                ← Back
            </button>
        </div>
    </div>
</template>

<style scoped>
.scene-four-container {
    width: 100%;
    height: 100vh;
    height: 100dvh;
    display: flex;
    justify-content: space-between;
    align-items: center;
    background-size: cover;
    background-position: center bottom;
    background-repeat: no-repeat;
    position: absolute;
    top: 0;
    left: 0;
    overflow: hidden;
    padding: 40px;
}

.scene-four-container.hide-cursor {
    cursor: none;
}

.computer-monitor {
    position: absolute;
    left: 8%;
    top: 45%;
    transform: translateY(-50%);
    display: flex;
    flex-direction: column;
    align-items: center;
    z-index: 5;
}

.monitor-frame {
    width: 1200px;
    height: 680px;
    background: linear-gradient(145deg, #2a2a2a, #1a1a1a);
    border-radius: 20px;
    padding: 15px;
    box-shadow: 0 25px 70px rgba(0, 0, 0, 0.4);
    position: relative;
}

.monitor-screen {
    width: 100%;
    height: calc(100% - 50px);
    background: #ffffff;
    border-radius: 10px;
    overflow: hidden;
    position: relative;
    box-shadow: inset 0 0 20px rgba(0, 0, 0, 0.1);
}

.screen-content {
    width: 100%;
    height: 100%;
    background: #ffffff;
    position: relative;
    padding-top: 2px;
    padding-bottom: 2px;
    display: flex;
    flex-direction: column;
}

.video-grid-container {
    width: 100%;
    height: calc(100% - 5px);
    overflow-x: auto;
    overflow-y: hidden;
    padding-top: 2px;
}

.video-grid-container::-webkit-scrollbar {
    height: 10px;
}

.video-grid-container::-webkit-scrollbar-track {
    background: #e0e0e0;
    border-radius: 5px;
    margin: 0 5px;
}

.video-grid-container::-webkit-scrollbar-thumb {
    background: #888;
    border-radius: 5px;
}

.video-grid-container::-webkit-scrollbar-thumb:hover {
    background: #555;
}

.video-grid {
    display: grid;
    grid-template-columns: repeat(4, minmax(250px, 1fr));
    gap: 8px;
    width: fit-content;
    min-width: 130%;
    height: 100%;
    padding-bottom: 5px;
}

.video-column {
    border-radius: 15px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    transition: all 0.3s ease;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
    position: relative;
    overflow: hidden;
    min-width: 250px;
}

.video-background {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    object-fit: cover;
    object-position: center;
    z-index: 1;
    filter: brightness(0.7);
}

.video-overlay {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    z-index: 2;
    transition: all 0.3s ease;
}

.video-column:hover .video-overlay {
    background: rgba(0, 0, 0, 0.3);
}

.video-column:hover {
    transform: translateY(-5px);
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.3);
}

.video-icon {
    font-size: 64px;
    color: white;
    margin-bottom: 15px;
    z-index: 3;
    text-shadow: 0 4px 12px rgba(0, 0, 0, 0.8);
    filter: drop-shadow(0 0 10px rgba(255, 255, 255, 0.3));
}

.video-name {
    font-size: 28px;
    font-weight: 900;
    color: white;
    text-align: center;
    z-index: 3;
    text-shadow: 0 4px 12px rgba(0, 0, 0, 0.9);
    letter-spacing: 2px;
    text-transform: uppercase;
}

.screen-cursor {
    position: absolute;
    font-size: 32px;
    transform: translate(-50%, -50%);
    pointer-events: none;
    z-index: 100;
    filter: drop-shadow(0 2px 4px rgba(0, 0, 0, 0.5));
    transition: all 0.05s ease-out;
}

.monitor-bezel-bottom {
    width: 100%;
    height: 50px;
    display: flex;
    justify-content: center;
    align-items: center;
}

.monitor-logo {
    width: 50px;
    height: 10px;
    background: #444;
    border-radius: 5px;
}

.monitor-stand {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-top: -10px;
}

.stand-neck {
    width: 70px;
    height: 60px;
    background: linear-gradient(145deg, #3a3a3a, #2a2a2a);
    border-radius: 10px;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
}

.stand-base {
    width: 200px;
    height: 18px;
    background: linear-gradient(145deg, #3a3a3a, #2a2a2a);
    border-radius: 50%;
    box-shadow: 0 5px 20px rgba(0, 0, 0, 0.4);
    margin-top: 5px;
}

.mouse-container {
    position: absolute;
    right: 15%;
    top: 50%;
    z-index: 10;
    transition: transform 0.15s ease-out;
}

.mouse {
    width: 90px;
    height: 130px;
    position: relative;
    filter: drop-shadow(0 12px 25px rgba(0, 0, 0, 0.25));
}

.mouse-top {
    width: 90px;
    height: 70px;
    background: linear-gradient(145deg, #f5f5f5, #d9d9d9);
    border-radius: 45px 45px 0 0;
    border: 3px solid #888;
    display: flex;
    justify-content: space-around;
    align-items: flex-start;
    padding: 10px 8px;
    position: relative;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.15);
}

.mouse-left-button {
    width: 32px;
    height: 50px;
    background: linear-gradient(145deg, #ffffff, #e6e6e6);
    border-radius: 25px 0 0 0;
    border-right: 2px solid #999;
}

.mouse-scroll-area {
    width: 14px;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding-top: 5px;
}

.mouse-scroll {
    width: 8px;
    height: 24px;
    background: #333;
    border-radius: 6px;
    box-shadow: inset 0 2px 5px rgba(0, 0, 0, 0.4);
}

.mouse-right-button {
    width: 32px;
    height: 50px;
    background: linear-gradient(145deg, #ffffff, #e6e6e6);
    border-radius: 0 25px 0 0;
    border-left: 2px solid #999;
}

.mouse-body {
    width: 106px;
    height: 60px;
    background: linear-gradient(145deg, #e0e0e0, #c0c0c0);
    border-radius: 0 0 45px 45px;
    border: 3px solid #888;
    border-top: none;
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2);
}

.back-button {
    position: absolute;
    bottom: 90px;
    left: 48px;
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

.video-fullscreen {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    height: 100dvh;
    background: #000000;
    z-index: 9999;
    display: flex;
    justify-content: center;
    align-items: center;
}

.fullscreen-video {
    width: 100%;
    height: 100%;
    object-fit: contain;
}

.close-video-button {
    position: absolute;
    top: 30px;
    right: 30px;
    padding: 15px 30px;
    background: rgba(231, 76, 60, 0.95);
    color: #ffffff;
    border: 3px solid #c0392b;
    border-radius: 12px;
    font-size: 20px;
    font-weight: 900;
    cursor: pointer;
    transition: all 0.3s ease;
    backdrop-filter: blur(5px);
    z-index: 10000;
    box-shadow: 0 6px 20px rgba(231, 76, 60, 0.4);
}

.close-video-button:hover {
    background: rgba(231, 76, 60, 1);
    transform: scale(1.1);
    box-shadow: 0 8px 25px rgba(231, 76, 60, 0.6);
}

.close-video-button:active {
    transform: scale(0.98);
}

@media (max-width: 1200px) {
    .monitor-frame {
        width: 550px;
        height: 380px;
    }

    .computer-monitor {
        left: 5%;
    }

    .mouse-container {
        right: 15%;
    }

    .screen-cursor {
        font-size: 24px;
    }

    .video-icon {
        font-size: 32px;
    }

    .video-name {
        font-size: 16px;
    }
}

@media (max-width: 1024px) {
    .monitor-frame {
        width: 480px;
        height: 340px;
    }

    .mouse {
        width: 75px;
        height: 110px;
    }

    .mouse-top {
        width: 75px;
        height: 60px;
    }

    .mouse-body {
        width: 88.3px;
        height: 50px;
    }

    .screen-cursor {
        font-size: 20px;
    }

    .video-grid {
        grid-template-columns: repeat(2, 1fr);
        grid-template-rows: repeat(2, 1fr);
    }
}

@media (max-width: 768px) {
    .monitor-frame {
        width: 400px;
        height: 280px;
    }

    .computer-monitor {
        left: 3%;
    }

    .mouse-container {
        right: 10%;
    }

    .mouse {
        width: 65px;
        height: 95px;
    }

    .mouse-top {
        width: 65px;
        height: 50px;
    }

    .mouse-body {
        width: 79.5px;
        height: 45px;
    }

    .mouse-left-button,
    .mouse-right-button {
        width: 26px;
        height: 40px;
    }

    .back-button {
        bottom: 30px;
        left: 30px;
        font-size: 18px;
        padding: 12px 24px;
    }

    .screen-cursor {
        font-size: 18px;
    }

    .video-icon {
        font-size: 24px;
    }

    .video-name {
        font-size: 14px;
    }

    .close-video-button {
        top: 20px;
        right: 20px;
        padding: 12px 24px;
        font-size: 18px;
    }
}

@media (max-width: 480px) {
    .monitor-frame {
        width: 300px;
        height: 220px;
    }

    .computer-monitor {
        left: 2%;
    }

    .mouse-container {
        right: 8%;
    }

    .mouse {
        width: 55px;
        height: 80px;
    }

    .mouse-top {
        width: 55px;
        height: 42px;
        padding: 6px;
    }

    .mouse-body {
        width: 55px;
        height: 38px;
    }

    .mouse-left-button,
    .mouse-right-button {
        width: 22px;
        height: 35px;
    }

    .mouse-scroll {
        width: 6px;
        height: 18px;
    }

    .back-button {
        bottom: 20px;
        left: 20px;
        font-size: 16px;
        padding: 10px 20px;
    }

    .screen-cursor {
        font-size: 16px;
    }
}
</style>