<script>
export default {
    name: 'SceneFive',
    emits: ['goBack', 'finish'],
    data() {
        return {
            backgroundImage: '/personajes/escenarios/escenario5.png',
            showFinalMessage: false
        }
    },
    methods: {
        handleGoBack() {
            this.$emit('goBack');
        },
        handleFinish() {
            this.showFinalMessage = true;
        },
        handleClose() {
            this.showFinalMessage = false;
        }
    }
}
</script>

<template>
    <div class="scene-five-container" :style="{ backgroundImage: `url(${backgroundImage})` }">

        <div v-if="showFinalMessage" class="final-message-overlay">
            <div class="final-message-box">
                <h1>Thank You for This Journey</h1>

                <div class="message-content">
                    <p class="intro">
                        You've witnessed the beauty and fragility of our planet. Every choice we make today shapes the
                        world of tomorrow.
                    </p>

                    <p class="emphasis">
                        Our Earth is home to over 8 million species, each playing a vital role in maintaining the
                        delicate balance of life. Yet, we're losing biodiversity at an unprecedented rate—up to 1,000
                        times faster than natural extinction rates.
                    </p>

                    <p>
                        Climate change isn't a distant threat—it's happening now. Rising temperatures, melting glaciers,
                        extreme weather events, and displaced communities are the reality millions face every day.
                    </p>

                    <p class="emphasis">
                        But here's the truth that gives us hope: <strong>We still have time to act.</strong>
                    </p>

                    <p>
                        Every tree planted, every piece of plastic refused, every conversation about climate action,
                        every sustainable choice—they all matter. You matter. Your actions create ripples that can
                        become waves of change.
                    </p>

                    <p class="call-to-action">
                        This isn't just about saving the planet—it's about preserving the home we share with countless
                        species, protecting the future of our children, and honoring the only world we have.
                    </p>

                    <p class="final-words">
                        The question isn't whether we can make a difference. The question is: Will we?
                    </p>

                    <p class="signature">
                        The future is not something we enter. The future is something we create.
                    </p>
                </div>

                <button class="close-button" @click="handleClose">
                    Close
                </button>
            </div>
        </div>

        <div class="button-container">
            <button class="back-button" @click="handleGoBack">
                ← Back
            </button>

            <button class="finish-button" @click="handleFinish">
                Finish ✓
            </button>
        </div>
    </div>
</template>

<style scoped>
.scene-five-container {
    width: 100%;
    height: 100vh;
    height: 100dvh;
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
    position: relative;
    overflow: hidden;
    display: flex;
    justify-content: center;
    align-items: center;
}

.button-container {
    position: absolute;
    bottom: 40px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    gap: 30px;
    z-index: 10;
}

.back-button,
.finish-button {
    padding: 18px 40px;
    border: 3px solid;
    border-radius: 12px;
    font-size: 22px;
    font-weight: 900;
    cursor: pointer;
    transition: all 0.3s ease;
    backdrop-filter: blur(5px);
    box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
}

.back-button {
    background: rgba(231, 76, 60, 0.95);
    color: #ffffff;
    border-color: #c0392b;
}

.back-button:hover {
    background: rgba(231, 76, 60, 1);
    transform: scale(1.1);
    box-shadow: 0 8px 25px rgba(231, 76, 60, 0.6);
}

.finish-button {
    background: rgba(46, 204, 113, 0.95);
    color: #ffffff;
    border-color: #27ae60;
}

.finish-button:hover {
    background: rgba(46, 204, 113, 1);
    transform: scale(1.1);
    box-shadow: 0 8px 25px rgba(46, 204, 113, 0.6);
}

.back-button:active,
.finish-button:active {
    transform: scale(0.98);
}

.final-message-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    height: 100dvh;
    background: rgba(0, 0, 0, 0.92);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 1000;
    padding: 20px;
    overflow-y: auto;
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

.final-message-box {
    background: linear-gradient(135deg, #1e3c72 0%, #2a5298 50%, #7e22ce 100%);
    padding: 50px;
    border-radius: 25px;
    max-width: 800px;
    width: 100%;
    color: white;
    animation: slideIn 0.6s ease-out;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5);
}

@keyframes slideIn {
    from {
        transform: translateY(-50px);
        opacity: 0;
    }

    to {
        transform: translateY(0);
        opacity: 1;
    }
}

.final-message-box h1 {
    font-size: 42px;
    margin-bottom: 30px;
    text-align: center;
    background: linear-gradient(to right, #ffd89b, #19547b);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    font-weight: 900;
}

.message-content {
    line-height: 1.8;
    font-size: 18px;
}

.message-content p {
    margin-bottom: 20px;
    text-align: justify;
}

.intro {
    font-size: 20px;
    font-weight: 600;
    color: #ffd89b;
}

.emphasis {
    background: rgba(255, 255, 255, 0.1);
    padding: 15px 20px;
    border-left: 4px solid #ffd89b;
    border-radius: 5px;
    font-weight: 600;
}

.call-to-action {
    font-size: 20px;
    font-weight: 700;
    color: #4ade80;
    text-align: center;
}

.final-words {
    font-size: 22px;
    font-weight: 800;
    text-align: center;
    color: #fbbf24;
    margin-top: 30px;
}

.signature {
    font-style: italic;
    text-align: center;
    font-size: 16px;
    opacity: 0.9;
    margin-top: 30px;
    border-top: 2px solid rgba(255, 255, 255, 0.3);
    padding-top: 20px;
}

.close-button {
    margin-top: 40px;
    padding: 18px 50px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 12px;
    font-size: 22px;
    font-weight: 900;
    cursor: pointer;
    transition: all 0.3s ease;
    display: block;
    margin-left: auto;
    margin-right: auto;
    box-shadow: 0 8px 25px rgba(0, 0, 0, 0.3);
}

.close-button:hover {
    transform: scale(1.1);
    box-shadow: 0 12px 35px rgba(102, 126, 234, 0.5);
}

.close-button:active {
    transform: scale(0.98);
}

@media (max-width: 768px) {
    .button-container {
        bottom: 20px;
        gap: 15px;
    }

    .back-button,
    .finish-button {
        padding: 14px 28px;
        font-size: 18px;
    }

    .final-message-box {
        padding: 30px 20px;
    }

    .final-message-box h1 {
        font-size: 32px;
    }

    .message-content {
        font-size: 16px;
    }

    .final-words {
        font-size: 18px;
    }
}
</style>