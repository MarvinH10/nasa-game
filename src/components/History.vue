<script>
export default {
    name: 'GameHistory',
    emits: ['videoComplete'],
    mounted() {
        this.$nextTick(() => {
            const video = this.$refs.historyVideo;
            if (video) {
                video.play().catch(err => {
                    console.error("Error reproduciendo video:", err);
                });
            }
        });
    },
    methods: {
        handleVideoEnd() {
            this.$emit('videoComplete');
        },
        handleSkip() {
            this.$emit('videoComplete');
        }
    }
}
</script>

<template>
    <div class="history-container">
        <video ref="historyVideo" class="history-video" @ended="handleVideoEnd" autoplay muted>
            <source src="/videos/historia.mp4" type="video/mp4">
            Tu navegador no soporta el elemento de video.
        </video>

        <button class="skip-button" @click="handleSkip">
            Skip ➤
        </button>
    </div>
</template>

<style scoped>
.history-container {
    position: relative;
    width: 100%;
    height: 100%;
    background: #000000;
    overflow: hidden;
}

.history-video {
    width: 100%;
    height: 100%;
    object-fit: cover;
    display: block;
}

.skip-button {
    position: absolute;
    bottom: 40px;
    right: 40px;
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
}

.skip-button:hover {
    background: rgba(39, 174, 96, 1);
    transform: scale(1.1);
    box-shadow: 0 4px 15px rgba(39, 174, 96, 0.5);
}

.skip-button:active {
    transform: scale(0.95);
}
</style>