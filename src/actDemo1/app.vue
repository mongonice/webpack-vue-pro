<template>
    <div id="app">
        <div class="recommend">
            <div class="recommend-wrap">
                <h1 @click="openModal">显示弹框</h1>
                <ul class="recommend-list">
                    <li v-for="n in 9" :key="n"></li>
                </ul>
            </div>
        </div>

        <c-modal></c-modal>
    </div>
</template>

<script>
import CTest from "../__common/components/c-test.js";
import { getList } from "./api/index.js";
Vue.use(CTest);

export default {
    name: "App",
    data() {
        return {
            content: "",
            mgs: "sss",
        };
    },

    template: "<div><p>我template出来的，年龄{{mgs}}</p></div>",

    mounted() {
        console.log(coreBus);
        console.log(utils);
        console.log(BOOL);

        // 使用CTest 来测试vue.runtime.esm.js  和 vue.esm.js
        this.$msg("123");

        let arr = Array.from([1, 2, 3]);
        console.log("hahah");
        console.log(arr);
        this._getList();
    },

    methods: {
        async _getList() {
            let res = await getList();
            console.log(res);
        },
        openModal() {
            console.log(123123);
            coreBus.$emit("toggleModal", true);
        },
    },

    components: {
        "c-modal": (_) => import("./components/modal.vue"),
    },
};
</script>

<style lang="scss">
li {
    list-style: none;
}
body,
html {
    height: 100%;
}
</style>
<style lang="scss" scoped>
#app {
    height: 100%;
}
.recommend {
    position: relative;
    width: 100%;
    height: 100%;
    background: url("./assets/images/bg-banner.webp") 0 0/ 100% auto no-repeat #f2f2f2;
}

.recommend-wrap {
    position: absolute;
    width: 914px;
    height: 1303px;
    background: #fff;
    border-radius: 40px;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
}

.recommend-list {
    display: flex;
    flex-wrap: wrap;
    padding: 0 62px;

    li {
        width: 33.333%;
        background: red;
        height: 40px;
        margin-bottom: 60px;
    }
}
</style>