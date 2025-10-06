<script>
import Phaser from "phaser";

export default {
    name: "GameLoader",
    emits: ['loadingComplete'],
    data() {
        return {
            game: null
        };
    },
    mounted() {
        this.$nextTick(() => {
            this.initGame();
        });
    },
    beforeUnmount() {
        if (this.game) {
            this.game.destroy(true);
            this.game = null;
        }
    },
    methods: {
        initGame() {
            const self = this;

            class PreloadScene extends Phaser.Scene {
                constructor() {
                    super("PreloadScene");
                    this.loadingTimer = null;
                    this.loadingLetters = [];
                }

                preload() {
                    this.load.on("loaderror", (file) => {
                        console.error("Error cargando:", file.src);
                    });

                    this.load.image("bear", "/images/oso.png");
                    this.load.image("background", "/images/fondo.png");

                    this.load.on("complete", () => {
                        this.startLoading();
                    });
                }

                startLoading() {
                    const centerX = this.scale.width / 2;
                    const centerY = this.scale.height / 2;

                    const bg = this.add.image(centerX, centerY, "background");
                    bg.setDisplaySize(this.scale.width, this.scale.height);

                    const loadingWord = "LOADING";
                    const dots = "...";
                    const fullText = loadingWord + dots;
                    const letterSpacing = 40;
                    const totalWidth = (fullText.length - 1) * letterSpacing;
                    const startX = centerX - totalWidth / 2;

                    for (let i = 0; i < fullText.length; i++) {
                        const letter = this.add.text(
                            startX + i * letterSpacing,
                            centerY - 120,
                            fullText[i],
                            {
                                font: "900 48px Arial",
                                fill: "#27ae60",
                                stroke: "#1a7a3e",
                                strokeThickness: 2
                            }
                        ).setOrigin(0.5);

                        this.loadingLetters.push(letter);

                        this.tweens.add({
                            targets: letter,
                            y: centerY - 140,
                            duration: 400,
                            ease: "Sine.easeInOut",
                            yoyo: true,
                            repeat: -1,
                            delay: i * 80
                        });
                    }

                    const barWidth = 400;
                    const barHeight = 40;
                    const barX = centerX - barWidth / 2;
                    const barY = centerY - barHeight / 2;
                    const borderRadius = 20;

                    let progressBox = this.add.graphics();
                    let progressBar = this.add.graphics();

                    progressBox.lineStyle(4, 0x2c3e50, 1);
                    progressBox.strokeRoundedRect(barX, barY, barWidth, barHeight, borderRadius);
                    progressBox.fillStyle(0xecf0f1, 1);
                    progressBox.fillRoundedRect(barX, barY, barWidth, barHeight, borderRadius);

                    const bear = this.add.image(barX, barY - 1, "bear");
                    bear.setScale(0.2);
                    bear.setOrigin(0.7, 1);
                    bear.setFlipX(true);

                    const percentText = this.add.text(centerX, centerY + 60, "0%", {
                        font: "900 28px Arial",
                        fill: "#ffffff",
                        stroke: "#2c3e50",
                        strokeThickness: 1
                    }).setOrigin(0.5);

                    let currentProgress = 0;
                    const loadingDuration = 5000;
                    const updateInterval = 50;
                    const progressIncrement = updateInterval / loadingDuration;

                    const updateProgress = () => {
                        if (!this.scene.isActive() || !this.sys.game) {
                            return;
                        }

                        currentProgress += progressIncrement;

                        if (currentProgress >= 1) {
                            currentProgress = 1;
                        }

                        progressBar.clear();

                        if (currentProgress > 0) {
                            const minWidth = barHeight - 4;
                            const maxWidth = barWidth - 4;
                            const fillWidth = minWidth + (maxWidth - minWidth) * currentProgress;

                            progressBar.fillStyle(0x50C878, 1);
                            progressBar.fillRoundedRect(
                                barX + 2,
                                barY + 2,
                                fillWidth,
                                barHeight - 4,
                                (barHeight - 4) / 2
                            );

                            bear.x = barX + 2 + fillWidth;

                            percentText.setText(Math.floor(currentProgress * 100) + "%");
                        }

                        if (currentProgress < 1) {
                            this.loadingTimer = setTimeout(updateProgress, updateInterval);
                        } else {
                            progressBar.destroy();
                            progressBox.destroy();
                            bear.destroy();

                            this.tweens.killAll();
                            this.loadingLetters.forEach(letter => letter.destroy());
                            this.loadingLetters = [];

                            percentText.setText("100%");
                            percentText.setStyle({
                                fill: "#27ae60",
                                font: "900 30px Arial",
                                stroke: "#ffffff",
                                strokeThickness: 1
                            });

                            const buttonWidth = 220;
                            const buttonHeight = 80;
                            const buttonX = centerX;
                            const buttonY = centerY - 120;

                            const buttonContainer = this.add.container(buttonX, buttonY);

                            const buttonBg = this.add.graphics();
                            buttonBg.fillStyle(0x27ae60, 1);
                            buttonBg.lineStyle(5, 0x1a7a3e, 1);
                            buttonBg.fillRoundedRect(
                                -buttonWidth / 2,
                                -buttonHeight / 2,
                                buttonWidth,
                                buttonHeight,
                                20
                            );
                            buttonBg.strokeRoundedRect(
                                -buttonWidth / 2,
                                -buttonHeight / 2,
                                buttonWidth,
                                buttonHeight,
                                20
                            );

                            const startText = this.add.text(0, 0, "START", {
                                font: "900 42px Arial",
                                fill: "#ffffff",
                                stroke: "#1a7a3e",
                                strokeThickness: 3
                            }).setOrigin(0.5);

                            buttonContainer.add([buttonBg, startText]);

                            const buttonZone = this.add.rectangle(
                                buttonX,
                                buttonY,
                                buttonWidth,
                                buttonHeight
                            ).setInteractive({ useHandCursor: true });

                            buttonZone.on("pointerover", () => {
                                this.tweens.add({
                                    targets: buttonContainer,
                                    scaleX: 1.12,
                                    scaleY: 1.12,
                                    duration: 250,
                                    ease: "Back.easeOut"
                                });
                            });

                            buttonZone.on("pointerout", () => {
                                this.tweens.add({
                                    targets: buttonContainer,
                                    scaleX: 1.0,
                                    scaleY: 1.0,
                                    duration: 250,
                                    ease: "Back.easeIn"
                                });
                            });

                            buttonZone.on("pointerdown", () => {
                                this.tweens.add({
                                    targets: buttonContainer,
                                    scaleX: 0.9,
                                    scaleY: 0.9,
                                    duration: 100,
                                    yoyo: true,
                                    onComplete: () => {
                                        const fadeOverlay = this.add.rectangle(
                                            0, 0,
                                            this.scale.width,
                                            this.scale.height,
                                            0x000000,
                                            0
                                        ).setOrigin(0);

                                        this.tweens.add({
                                            targets: fadeOverlay,
                                            alpha: 1,
                                            duration: 500,
                                            onComplete: () => {
                                                self.$emit('loadingComplete');
                                            }
                                        });
                                    }
                                });
                            });
                        }
                    };

                    this.loadingTimer = setTimeout(updateProgress, 100);
                }

                shutdown() {
                    if (this.loadingTimer) {
                        clearTimeout(this.loadingTimer);
                        this.loadingTimer = null;
                    }
                    if (this.tweens) {
                        this.tweens.killAll();
                    }
                    if (this.loadingLetters) {
                        this.loadingLetters.forEach(letter => {
                            if (letter && letter.destroy) {
                                letter.destroy();
                            }
                        });
                        this.loadingLetters = [];
                    }
                }
            }

            const container = document.getElementById("game-loader");
            const width = Math.max(container.clientWidth || 800, 1);
            const height = Math.max(container.clientHeight || 600, 1);

            const config = {
                type: Phaser.AUTO,
                width: width,
                height: height,
                parent: "game-loader",
                scene: [PreloadScene],
                scale: {
                    mode: Phaser.Scale.RESIZE,
                    autoCenter: Phaser.Scale.CENTER_BOTH,
                    min: {
                        width: 320,
                        height: 240
                    }
                },
                render: {
                    antialias: true,
                    pixelArt: false,
                    roundPixels: false
                },
                audio: {
                    noAudio: true
                }
            };

            this.game = new Phaser.Game(config);
        }
    }
};
</script>

<template>
    <div id="game-loader"></div>
</template>

<style scoped>
#game-loader {
    width: 100%;
    height: 100%;
    overflow: hidden;
}
</style>