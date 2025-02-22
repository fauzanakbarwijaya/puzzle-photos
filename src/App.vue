<script setup>
    import {
        ref,
        computed,
        onMounted
    } from 'vue';

    const imageUrl = ref(null);
    const fixedImageUrl = ref(null); // Menyimpan URL gambar tetap untuk puzzle
    const gridSize = ref(3);
    const puzzlePieces = ref([]);
    const shuffledPieces = ref([]);
    const gameStarted = ref(false);
    const canvasSize = 300;
    const pieceSize = computed(() => canvasSize / gridSize.value);
    const timer = ref(300); // Timer dalam detik (5 menit)
    const firstMoveMade = ref(false);
    const selectedPiece = ref(null);
    let timerInterval = null;

    const handleImageUpload = (event) => {
        const file = event.target.files[0];
        if (file) {
            const reader = new FileReader();
            reader.onload = () => {
                imageUrl.value = reader.result;
                fixedImageUrl.value = reader.result; // Simpan sebagai gambar tetap untuk puzzle
            };
            reader.readAsDataURL(file);
        }
    };

    const generateRandomImage = async () => {
        try {
            const response = await fetch(`https://picsum.photos/600/600?random=${Math.random()}`, {
                mode: 'cors'
            });
            const blob = await response.blob();
            const reader = new FileReader();

            reader.onload = () => {
                imageUrl.value = reader.result;
                fixedImageUrl.value = reader.result;
            };

            reader.readAsDataURL(blob);
        } catch (error) {
            console.error('Error fetching image:', error);
        }
    };


    const createPuzzle = () => {
        if (!fixedImageUrl.value) return;
        gameStarted.value = true;
        firstMoveMade.value = false;
        timer.value = 300;
        puzzlePieces.value = [];
        clearInterval(timerInterval);

        const img = new Image();
        img.crossOrigin = 'anonymous';
        img.src = fixedImageUrl.value; // Gunakan gambar tetap
        img.onload = () => {
            for (let row = 0; row < gridSize.value; row++) {
                for (let col = 0; col < gridSize.value; col++) {
                    const canvas = document.createElement('canvas');
                    canvas.width = pieceSize.value;
                    canvas.height = pieceSize.value;
                    const ctx = canvas.getContext('2d');
                    ctx.drawImage(
                        img,
                        col * (img.width / gridSize.value),
                        row * (img.height / gridSize.value),
                        img.width / gridSize.value,
                        img.height / gridSize.value,
                        0,
                        0,
                        pieceSize.value,
                        pieceSize.value
                    );
                    puzzlePieces.value.push({
                        id: row * gridSize.value + col,
                        imgSrc: canvas.toDataURL()
                    });
                }
            }
            shufflePuzzle();
        };
    };

    const shufflePuzzle = () => {
        shuffledPieces.value = [...puzzlePieces.value].sort(() => Math.random() - 0.5);
    };

    const startTimer = () => {
        if (!firstMoveMade.value) {
            firstMoveMade.value = true;
            timerInterval = setInterval(() => {
                if (timer.value > 0) {
                    timer.value--;
                } else {
                    clearInterval(timerInterval);
                    alert('Waktu habis! Coba lagi.');
                    gameStarted.value = false;
                }
            }, 1000);
        }
    };

    const checkWin = () => {
        if (shuffledPieces.value.every((piece, index) => piece.id === puzzlePieces.value[index].id)) {
            clearInterval(timerInterval);
            alert('Selamat! Anda menyelesaikan puzzle.');
            gameStarted.value = false;
        }
    };

    const onDragStart = (event, index) => {
        event.dataTransfer.setData('index', index);
        startTimer();
    };

    const onDrop = (event, dropIndex) => {
        event.preventDefault();
        const dragIndex = parseInt(event.dataTransfer.getData('index'));
        if (!isNaN(dragIndex)) {
            [shuffledPieces.value[dragIndex], shuffledPieces.value[dropIndex]] = [shuffledPieces.value[dropIndex],
                shuffledPieces.value[dragIndex]
            ];
            checkWin();
        }
    };

    const onDragOver = (event) => {
        event.preventDefault();
    };

    const onPieceClick = (index) => {
        if (selectedPiece.value === null) {
            selectedPiece.value = index; // Pilih pertama
        } else if (selectedPiece.value !== index) {
            swapPieces(selectedPiece.value, index); // Swap jika beda
            selectedPiece.value = null; // Reset setelah swap
        }
    };



    const swapPieces = (index1, index2) => {
        [shuffledPieces.value[index1], shuffledPieces.value[index2]] = [shuffledPieces.value[index2], shuffledPieces
            .value[index1]
        ];
        checkWin();
    };
</script>

<template>
    <div class="container vh-100 d-flex justify-content-center align-items-center text-center">
        <div class="card p-4 bg-dark text-light shadow-lg">
            <h2 class="mb-4">Puzzle Photos</h2>

            <div class="mb-3">
                <input type="file" @change="handleImageUpload" class="form-control bg-transparent text-light" />
            </div>

            <button @click="generateRandomImage" class="btn btn-outline-success mb-3">Generate Random Image</button>

            <div v-if="imageUrl" class="mb-3">
                <img :src="imageUrl" alt="Uploaded Image" class="img-fluid rounded"
                    style="max-width: 300px; border: 2px solid white;" />
            </div>

            <div>
                <label class="form-label">Pilih Ukuran Puzzle:</label>
                <select v-model="gridSize" class="form-select w-auto mx-auto bg-transparent text-light border-light">
                    <option :value="3">3x3</option>
                    <option :value="4">4x4</option>
                    <option :value="5">5x5</option>
                </select>
            </div>
            <p class="mt-3">Tap To Swap for mobile OR Drag&Drop for desktop</p>
            <button @click="createPuzzle" class="btn btn-outline-primary mt-2">Mulai</button>

            <div class="mt-4 text-light" v-if="gameStarted">Waktu tersisa:
                {{ Math . floor(timer / 60) }}:{{ timer % 60 . toString() . padStart(2, '0') }}</div>

            <div class="mt-4 d-flex flex-wrap" :style="{ width: canvasSize + 'px', height: canvasSize + 'px' }"
                v-if="gameStarted">
                <div v-for="(piece, index) in shuffledPieces" :key="piece.id" class="puzzle-piece"
                    :class="{ 'selected-piece': selectedPiece === index }" draggable="true"
                    @dragstart="onDragStart($event, index)" @dragover="onDragOver" @drop="onDrop($event, index)"
                    @click="onPieceClick(index)"
                    :style="{
                        width: pieceSize + 'px',
                        height: pieceSize + 'px',
                        backgroundImage: `url(${piece.imgSrc})`,
                        backgroundSize: 'cover'
                    }">
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
    .card {
        background: rgba(0, 0, 0, 0.8);
        border-radius: 12px;
    }

    .puzzle-piece {
        display: inline-block;
        border: 1px solid #fff;
        cursor: grab;
    }

    .selected-piece {
        outline: 3px solid yellow;
        box-shadow: 0 0 10px yellow;
    }
</style>
