<template>
    <div id="app">
        <Loading
            :active.sync="waiting"
            :is-full-screen="true"
            :can-cancel="false"
        ></Loading>
        <div class="screen">
            <div>Your color is Blue</div>
            <div v-if="!this.done">
                Turn : {{ 59 - available_actions.length }}
            </div>
            <div v-else>
                <div v-if="this.score[0] > this.score[1]">Winner is Blue</div>
                <div v-else-if="this.score[0] < this.score[1]">
                    Winner is Red
                </div>
                <div v-else>引き分けです</div>
            </div>

            <table align="center">
                <tr v-for="x in 9" :key="x">
                    <td
                        v-for="y in 13"
                        :key="10 * y + x"
                        @click="set_bar(x, y)"
                    >
                        <Item
                            :type="get_type(x, y)"
                            :color="board[x - 1][y - 1]"
                        ></Item>
                    </td>
                </tr>
            </table>
        </div>
    </div>
</template>

<script>
import Item from "@/components/Item.vue";
import axios from "axios";
import Loading from "vue-loading-overlay";
import "vue-loading-overlay/dist/vue-loading.css";

export default {
    name: "App",
    components: {
        Item,
        Loading,
    },
    data: function () {
        return {
            board: [],
            turn: -1,
            score: [0, 0],
            available_actions: [...Array(58).keys()],
            done: false,
            turn_cnt: 1,
            waiting: false,
        };
    },
    methods: {
        init_board() {
            var new_board = [];
            for (var x = 0; x < 9; x++) {
                var boardLine = [];
                for (var y = 0; y < 13; y++) {
                    boardLine.push(0);
                }
                new_board.push(boardLine);
            }
            this.board = new_board;
        },
        get_type(x, y) {
            if (this.is_dot(x, y)) {
                return 0;
            } else if (this.is_box(x, y)) {
                return 1;
            } else if (this.is_bar(x, y)) {
                if (x % 2 == 0) {
                    return 2;
                } else {
                    return 3;
                }
            } else {
                throw "Unexpected type";
            }
        },
        set_bar(x, y) {
            if (
                this.waiting ||
                !this.is_bar(x, y) ||
                this.board[x - 1][y - 1] != 0
            ) {
                return;
            }
            const url = process.env.VUE_APP_API_URL + "/step";
            this.waiting = true;
            axios
                .post(url, {
                    board: this.board,
                    score: this.score,
                    turn: this.turn,
                    available_actions: this.available_actions,
                    action: [x - 1, y - 1],
                })
                .then((response) => {
                    this.board = JSON.parse(response.data["board"]);
                    this.turn = JSON.parse(response.data["turn"]);
                    this.available_actions = JSON.parse(
                        response.data["available_actions"]
                    );
                    this.score = JSON.parse(response.data["score"]);
                    this.done = JSON.parse(response.data["done"]);
                    this.waiting = false;
                });
        },
        is_bar(x, y) {
            if (x % 2 != y % 2) {
                return true;
            } else {
                return false;
            }
        },
        is_dot(x, y) {
            if (x % 2 == 1 && y % 2 == 1) {
                return true;
            } else {
                return false;
            }
        },
        is_box(x, y) {
            if (x % 2 == 0 && y % 2 == 0) {
                return true;
            } else {
                return false;
            }
        },
        copy_board() {
            return JSON.parse(JSON.stringify(this.board));
        },
        wake_server() {
            this.waiting = true;
            const url = process.env.VUE_APP_API_URL;
            axios.get(url).then(() => {
                this.waiting = false;
            });
        },
    },
    created() {
        this.init_board();
    },
    mounted() {
        this.wake_server();
    },
};
</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
}
</style>
