<template>
    <div class="video-container">
        <video ref="video" autoplay style="display: none;"></video>
        <canvas ref="canvas"></canvas>
    </div>
    <div class="device-info">
        <p>ビデオデバイス名: {{ videoDeviceName }}</p>
    </div>
    <div class="radio-container">

        <input type="radio" id="1717" v-model="selectedOption" :value="options.size1717">
        <label for="1717">1717</label>

        <input type="radio" id="1417" v-model="selectedOption" :value="options.size1417">
        <label for="1417">1417</label>

        <input type="radio" id="1012" v-model="selectedOption" :value="options.size1012">
        <label for="1012">1012</label>

    </div>
</template>

<script>
export default {
    name: 'CameraComponent',

    data() {
        return {
            selectedOption: null,
            videoDeviceName: null,
            options: {
                size1717: { x: 225, y: 150, width: 200, height: 200, color: 'white', lineWidth: 5 },
                size1417: { x: 250, y: 150, width: 150, height: 200, color: 'white', lineWidth: 5 },
                size1012: { x: 275, y: 175, width: 100, height: 150, color: 'white', lineWidth: 5 },
            },
        };
    },

    mounted() {
        this.selectedOption = this.options.size1417;

        if (navigator.mediaDevices.getUserMedia) {
            navigator.mediaDevices.getUserMedia({ video: true })
                .then((stream) => {
                    this.$refs.video.srcObject = stream;
                    this.$refs.video.addEventListener('play', () => {
                        const canvas = this.$refs.canvas;
                        canvas.width = this.$refs.video.videoWidth;
                        canvas.height = this.$refs.video.videoHeight;
                        const ctx = canvas.getContext('2d');
                        const drawRect = (x, y, w, h, color, lineWidth) => {
                            ctx.beginPath();
                            ctx.rect(x, y, w, h);
                            ctx.strokeStyle = color;
                            ctx.lineWidth = lineWidth;
                            ctx.stroke();
                        };
                        const drawDashedRect = (x, y, w, h, color, lineWidth, dashLength) => {
                            ctx.beginPath();
                            ctx.setLineDash([dashLength, dashLength]);
                            ctx.rect(x, y, w, h);
                            ctx.strokeStyle = color;
                            ctx.lineWidth = lineWidth;
                            ctx.stroke();
                            ctx.setLineDash([]);
                        };
                        const draw = () => {
                            ctx.drawImage(this.$refs.video, 0, 0, canvas.width, canvas.height);
                            // drawRect(250, 150, 150, 200, 'white', 5);
                            drawRect(
                                this.selectedOption.x,
                                this.selectedOption.y,
                                this.selectedOption.width,
                                this.selectedOption.height,
                                this.selectedOption.color,
                                this.selectedOption.lineWidth
                            );

                            drawDashedRect(
                                this.selectedOption.x + 25,
                                this.selectedOption.y + 25,
                                this.selectedOption.width - 50,
                                this.selectedOption.height - 50,
                                this.selectedOption.color,
                                this.selectedOption.lineWidth,
                                10);
                            requestAnimationFrame(draw);
                        };
                        requestAnimationFrame(draw);
                        this.getVideoDeviceName(stream);
                    });
                })
                .catch((error) => {
                    console.log("Something went wrong!", error);
                });
        }
    },

    methods: {
        // ビデオデバイス名を取得するメソッド
        getVideoDeviceName(stream) {
            const videoTracks = stream.getVideoTracks();
            if (videoTracks.length > 0) {
                this.videoDeviceName = videoTracks[0].label;
                console.log("Video device label:", this.videoDeviceName);
            } else {
                console.log("No video tracks found in the stream.");
            }
        },
    },
}
</script>


<style scoped>
.video-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    /* position: relative; */
}

.radio-container {
    display: flex;
    flex-direction: row;
    /* 横に並べるように修正 */
    justify-content: center;
    /* 必要に応じて余白を調整 */
    align-items: center;
    margin-top: 30px;
    /* 縦の間隔を調整 */
}


.radio-container input[type="radio"] {
    margin-left: 30px
}

.device-info {
    margin-top: 10px;
    display: flex;
    flex-direction: column;
    align-items: center;
}
</style>


