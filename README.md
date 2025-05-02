<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Súmula de Voleibol</title>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            padding: 10px;
        }

        .court {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 5px;
            margin: 20px 0;
            background: #e0e0e0;
            padding: 10px;
            border-radius: 10px;
        }

        .position {
            background: white;
            border: 1px solid #ccc;
            padding: 10px;
            text-align: center;
            min-height: 60px;
        }

        .serving {
            border: 3px solid #2196F3;
        }

        .team-section {
            margin: 20px 0;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
        }

        .score {
            font-size: 2em;
            font-weight: bold;
            text-align: center;
            margin: 10px 0;
        }

        .controls {
            display: flex;
            gap: 10px;
            justify-content: center;
            margin: 20px 0;
        }

        button {
            padding: 10px 20px;
            background: #2196F3;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }

        input, select {
            padding: 5px;
            margin: 5px;
            width: 150px;
        }
    </style>
</head>
<body>
    <div id="app">
        <!-- Configuração Inicial -->
        <div v-if="!gameStarted">
            <div class="team-section">
                <h2>Time A</h2>
                <input v-model="teamA.name" placeholder="Nome do Time A">
                <h3>Titulares</h3>
                <div v-for="(player, index) in teamA.players" :key="index">
                    <input v-model="player.name" placeholder="Nome">
                    <input v-model="player.number" type="number" placeholder="Número">
                    <select v-model="player.position">
                        <option v-for="n in 6" :value="n">Posição {{n}}</option>
                    </select>
                </div>
            </div>

            <div class="team-section">
                <h2>Time B</h2>
                <input v-model="teamB.name" placeholder="Nome do Time B">
                <h3>Titulares</h3>
                <div v-for="(player, index) in teamB.players" :key="index">
                    <input v-model="player.name" placeholder="Nome">
                    <input v-model="player.number" type="number" placeholder="Número">
                    <select v-model="player.position">
                        <option v-for="n in 6" :value="n">Posição {{n}}</option>
                    </select>
                </div>
            </div>

            <button @click="startGame">Iniciar Jogo</button>
        </div>

        <!-- Jogo em Andamento -->
        <div v-else>
            <div class="score">
                {{teamA.name}}: {{teamA.score}} | {{teamB.name}}: {{teamB.score}}
            </div>

            <div class="controls">
                <button @click="addPoint('A')">+1 {{teamA.name}}</button>
                <button @click="addPoint('B')">+1 {{teamB.name}}</button>
            </div>

            <div :class="['team-section', {serving: servingTeam === 'A'}]">
                <h2>{{teamA.name}}</h2>
                <div class="court">
                    <div class="position" v-for="pos in rotatedPlayersA">
                        {{pos.name}}<br>#{{pos.number}}
                    </div>
                </div>
            </div>

            <div :class="['team-section', {serving: servingTeam === 'B'}]">
                <h2>{{teamB.name}}</h2>
                <div class="court">
                    <div class="position" v-for="pos in rotatedPlayersB">
                        {{pos.name}}<br>#{{pos.number}}
                    </div>
                </div>
            </div>

            <button @click="newSet">Novo Set</button>
        </div>
    </div>

    <script>
        const { createApp } = Vue;

        createApp({
            data() {
                return {
                    gameStarted: false,
                    servingTeam: 'A',
                    teamA: {
                        name: '',
                        score: 0,
                        players: Array(6).fill().map(() => ({ name: '', number: null, position: null })),
                        rotationHistory: []
                    },
                    teamB: {
                        name: '',
                        score: 0,
                        players: Array(6).fill().map(() => ({ name: '', number: null, position: null })),
                        rotationHistory: []
                    }
                }
            },
            computed: {
                rotatedPlayersA() {
                    return this.rotatePlayers([...this.teamA.players]);
                },
                rotatedPlayersB() {
                    return this.rotatePlayers([...this.teamB.players]);
                }
            },
            methods: {
                startGame() {
                    this.gameStarted = true;
                    this.servingTeam = 'A';
                },
                
                addPoint(team) {
                    if(team === 'A') {
                        this.teamA.score++;
                        if(this.servingTeam !== 'A') {
                            this.rotateTeam('B');
                            this.servingTeam = 'A';
                        }
                    } else {
                        this.teamB.score++;
                        if(this.servingTeam !== 'B') {
                            this.rotateTeam('A');
                            this.servingTeam = 'B';
                        }
                    }
                },

                rotateTeam(team) {
                    const players = this[`team${team}`].players;
                    players.unshift(players.pop());
                    this[`team${team}`].rotationHistory.push([...players]);
                },

                rotatePlayers(players) {
                    return players.sort((a, b) => a.position - b.position);
                },

                newSet() {
                    this.teamA.score = 0;
                    this.teamB.score = 0;
                    this.servingTeam = 'A';
                    this.teamA.rotationHistory = [];
                    this.teamB.rotationHistory = [];
                }
            }
        }).mount('#app');
    </script>
</body>
</html>
