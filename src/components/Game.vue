<template>
    <div @mousemove="updateMousePosition">
        <h1>{{ score < 1000 ? Math.round(score) : transformK(score) }}</h1>
        <button @click="click" id="click">Click</button>
        <div>{{ scorePerSecond<1000 ? scorePerSecond : transformK(scorePerSecond) }}/sec</div>
        <div>
            <button @click="save" style="background-color: green;">Save</button>
            <button @click="reset" style="background-color: red;">Reset</button>
        </div>
        <div id="magasin">
            <button @click="buyAuto1" :style="score>=calculatedPrice1 ? 'background-color: #4caf50' : 'background-color: #f44336'">AutoClicker 1 {{ calculatedPrice1 < 1000 ? calculatedPrice1 : transformK(calculatedPrice1) }} {{ autoClic }}</button>
            <button v-if="scorePerSecond>=3" @click="buyAuto2" :style="score>=calculatedPrice2 ? 'background-color: #4caf50' : 'background-color: #f44336'">AutoClicker 2 {{ calculatedPrice2 < 1000 ? calculatedPrice2 : transformK(calculatedPrice2) }} {{ autoClic2 }}</button>
            <button v-if="scorePerSecond>=25" @click="buyAuto3" :style="score>=calculatedPrice3 ? 'background-color: #4caf50' : 'background-color: #f44336'">AutoClicker 3 {{ calculatedPrice3 < 1000 ? calculatedPrice3 : transformK(calculatedPrice3) }} {{ autoClic3 }}</button>
            <button v-if="scorePerSecond>=5" @click="buyUpdate" :style="score>=calculatedPriceUpdate ? 'background-color: #4caf50' : 'background-color: #f44336'">Update {{ calculatedPriceUpdate < 1000 ? calculatedPriceUpdate : transformK(calculatedPriceUpdate) }} {{ updateClick }}</button>
        </div>
        
        <div v-for="popup in popups" :key="popup.id" :style="{ top: popup.y + 'px', left: popup.x + 'px' }" class="popup">
            +{{ popup.value }}
        </div>
    </div>
</template>

<script>
export default {
    name: 'Game-Main',
    data() {
        return {
            score: 0,
            autoClic: 0,
            autoClic2: 0,
            autoClic3: 0,
            updateClick: 1,
            initialPrice1: 10,
            initialPrice2: 100,
            initialPrice3: 1000,
            initialPriceUpdate: 25,
            multiplierUpdate: 2,
            multiplier: 1.4,
            mouseX: 0,   // Position X de la souris
            mouseY: 0,   // Position Y de la souris
            popups: []   // Liste des popups pour l'animation
        }
    },
    computed: {
        calculatedPrice1() {
            return Math.floor(this.initialPrice1 * Math.pow(this.multiplier, this.autoClic));
        },
        calculatedPrice2() {
            return Math.floor(this.initialPrice2 * Math.pow(this.multiplier, this.autoClic2));
        },
        calculatedPrice3() {
            return Math.floor(this.initialPrice3 * Math.pow(this.multiplier, this.autoClic3));
        },
        calculatedPriceUpdate() {
            return Math.floor(this.initialPriceUpdate * Math.pow(this.multiplierUpdate, this.updateClick));
        },
        scorePerSecond() {
            return this.autoClic/2 + this.autoClic2*5 + this.autoClic3*50
        }
    },
    methods: {
        click() {
            this.score += this.updateClick;
            this.showPopup(this.updateClick);
        },
        save() {
            const game = {
                score: this.score,
                autoClic: this.autoClic,
                autoClic2: this.autoClic2,
                autoClic3: this.autoClic3,
                updateClick: this.updateClick
            };
            localStorage.setItem('game', JSON.stringify(game));
        },
        reset() {
            this.score = 0;
            this.autoClic = 0;
            this.autoClic2 = 0;
            this.autoClic3 = 0;
            this.updateClick = 1;
            this.save();
        },
        buyAuto1() {
            const price1 = this.calculatedPrice1;
            if (this.score >= price1) {
                this.score -= price1;
                this.autoClic++;
                this.save();
            }
        },
        buyAuto2() {
            const price2 = this.calculatedPrice2;
            if (this.score >= price2) {
                this.score -= price2;
                this.autoClic2++;
                this.save();
            }
        },
        transformK(value){
            return (Math.round(value/100))/10+"k"
        },
        buyAuto3() {
            const price3 = this.calculatedPrice3;
            if (this.score >= price3) {
                this.score -= price3;
                this.autoClic3++;
                this.save();
            }
        },
        buyUpdate() {
            const priceUpdate = this.calculatedPriceUpdate;
            if (this.score >= priceUpdate) {
                this.score -= priceUpdate;
                this.updateClick++;
                this.save();
            }
        },
        updateMousePosition(event) {
            this.mouseX = event.clientX;
            this.mouseY = event.clientY;
        },
        showPopup(value) {
            const popup = {
                id: Date.now(),
                x: this.mouseX+15,
                y: this.mouseY,
                value: value
            };
            this.popups.push(popup);
            setTimeout(() => {
                this.popups = this.popups.filter(p => p.id !== popup.id);
            }, 1000);
        }
    },
    mounted() {
        const savedGame = localStorage.getItem('game');
        if (savedGame) {
            const game = JSON.parse(savedGame);
            this.score = game.score;
            this.autoClic = game.autoClic;
            this.autoClic2 = game.autoClic2;
            this.autoClic3 = game.autoClic3;
            this.updateClick = game.updateClick;
        }
        setInterval(() => {
            this.score += this.autoClic / 20; //0.5/s
            this.score += this.autoClic2 / 2; //5/s
            this.score += this.autoClic3 * 5; //50/s
        }, 100);
    }
}
</script>

<style>
#click {
    width: 200px;
    height: 200px;
    margin-bottom: 50px;
    background-color: #4caf50;
    border-radius: 50%;
    text-align: center;
    line-height: 200px;
    font-size: 50px;
    color: white;
    border: none;
    box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
    animation: bounce 2s infinite ease-in-out;
    transition: transform 0.2s, background-color 0.2s;
}

#click:hover {
    background-color: #45a049;
    transform: scale(1.1);
    box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
}

button {
    color: white;
    padding: 10px 20px;
    margin: 5px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.2s, transform 0.2s;
}

button:hover {
    background-color: #007bb5;
    transform: scale(1.05);
}

.popup {
    position: absolute;
    font-size: 20px;
    font-weight: bold;
    color: #ff4500;
    animation: fade 1s ease-out;
}

@keyframes fade {
    0% {
        opacity: 1;
        transform: translateY(0);
    }
    100% {
        opacity: 0;
        transform: translateY(-30px);
    }
}
</style>
