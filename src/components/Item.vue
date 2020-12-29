<template>
    <img :src="content" width="50" height="50" />
</template>

<script>
export default {
    data() {
        return {
            black_box: require("@/assets/black_box.png"),
            black_horizontal: require("@/assets/black_horizontal.png"),
            black_vertical: require("@/assets/black_vertical.png"),
            blue_box: require("@/assets/blue_box.png"),
            blue_horizontal: require("@/assets/blue_horizontal.png"),
            blue_vertical: require("@/assets/blue_vertical.png"),
            red_box: require("@/assets/red_box.png"),
            red_horizontal: require("@/assets/red_horizontal.png"),
            red_vertical: require("@/assets/red_vertical.png"),
            dot: require("@/assets/dot.png"),
        };
    },
    props: {
        type: Number,
        color: Number,
    },
    computed: {
        content() {
            var path;
            switch (this.type) {
                case 0:
                    path = this.dot;
                    break;
                case 1:
                    switch (this.color) {
                        case -1:
                            path = this.blue_box;
                            break;
                        case 0:
                            path = this.black_box;
                            break;
                        case 1:
                            path = this.red_box;
                            break;
                    }
                    break;
                case 2:
                    switch (this.color) {
                        case -1:
                            path = this.blue_vertical;
                            break;
                        case 0:
                            path = this.black_vertical;
                            break;
                        case 1:
                            path = this.red_vertical;
                            break;
                    }
                    break;
                case 3:
                    switch (this.color) {
                        case -1:
                            path = this.blue_horizontal;
                            break;
                        case 0:
                            path = this.black_horizontal;
                            break;
                        case 1:
                            path = this.red_horizontal;
                            break;
                    }
                    break;
                default:
                    throw "Unexpected type";
            }
            return path;
        },
    },
};
</script>

<style scoped>
</style>
