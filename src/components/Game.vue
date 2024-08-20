<template>
    <div>
        <h1>{{ Math.round(score) }}</h1>
        <button @click="click" id="click">Click</button>
        <div>
            <button @click="save">Save</button>
            <button @click="reset">Reset</button>
        </div>
        <div id="magasin">
            <button @click="buyAuto1">AutoClicker 1 {{ calculatedPrice1 }} {{ autoClic }}</button>
            <button @click="buyAuto2">AutoClicker 2 {{ calculatedPrice2 }} {{ autoClic2 }}</button>
            <button @click="buyAuto3">AutoClicker 3 {{ calculatedPrice3 }} {{ autoClic3 }}</button>
            <button @click="buyUpdate">Update {{ calculatedPriceUpdate }} {{ updateClick }}</button>
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
            multiplier: 1.4
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
        }
    },
    methods: {
        click() {
            this.score += this.updateClick;
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
            this.score += this.autoClic / 10;
            this.score += this.autoClic2 * 1;
            this.score += this.autoClic3 * 10;
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

@keyframes bounce {
    0%, 100% {
        transform: translateY(0);
    }
    50% {
        transform: translateY(-20px);
    }
}

button {
    background-color: #008cba;
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
</style>
