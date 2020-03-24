<template>
    <div class="sidebar">
        <el-menu
            class="sidebar-el-menu"
            :default-active="onRoutes"
            :collapse="collapse"
            background-color="#324157"
            text-color="#bfcbd9"
            active-text-color="#20a0ff"
            unique-opened
            router
        >
            <template v-for="item in items">
                <template v-if="item.subs">
                    <el-submenu :index="item.index" :key="item.index">
                        <template slot="title">
                            <i :class="item.icon"></i>
                            <span slot="title">{{ item.title }}</span>
                        </template>
                        <template v-for="subItem in item.subs">
                            <el-submenu
                                v-if="subItem.subs"
                                :index="subItem.index"
                                :key="subItem.index"
                            >
                                <template slot="title">{{ subItem.title }}</template>
                                <!-- <el-menu-item
                                    v-for="(threeItem,i) in subItem.subs"
                                    :key="i"
                                    :index="threeItem.index"
                                >{{ threeItem.title }}</el-menu-item> -->
                                <template v-for="threeItem in subItem.subs">
                                    <el-submenu
                                        v-if="threeItem.subs"
                                        :index="threeItem.index"
                                        :key="threeItem.index"
                                    >
                                        <template slot="title">{{ threeItem.title }}</template>
                                        <el-menu-item
                                            v-for="(fourItem,i) in threeItem.subs"
                                            :key="i"
                                            :index="fourItem.index"
                                        >{{ fourItem.title }}</el-menu-item>
                                    </el-submenu>
                                    <el-menu-item
                                        v-else
                                        :index="threeItem.index"
                                        :key="threeItem.index"
                                    >{{ threeItem.title }}</el-menu-item>
                                </template>
                            </el-submenu>
                            <el-menu-item
                                v-else
                                :index="subItem.index"
                                :key="subItem.index"
                            >{{ subItem.title }}</el-menu-item>
                        </template>
                    </el-submenu>
                </template>
                <template v-else>
                    <el-menu-item :index="item.index" :key="item.index">
                        <i :class="item.icon"></i>
                        <span slot="title">{{ item.title }}</span>
                    </el-menu-item>
                </template>
            </template>
        </el-menu>
    </div>
</template>

<script>
import bus from '../common/bus';
import axios from 'axios';
export default {
    data() {
        return {
            collapse: false,
            items: [],
        };
    },
    computed: {
        onRoutes() {
            return this.$route.path.replace('/', '');
        }
    },
    created() {
        // 通过 Event Bus 进行组件间通信，来折叠侧边栏
        bus.$on('collapse', msg => {
            this.collapse = msg;
            bus.$emit('collapse-content', msg);
        });
        this.getDataInfo();
    },
    methods:{
        getDataInfo(){
            var storage=window.localStorage
            var account=storage.zhjx_account
            var fd = new FormData()
            fd.append("flag","Sidebar")
            fd.append("account",account)
            axios.post(`${this.baseURL}/common/sidebar.php`,fd).then((res)=> {  //ES6写法
                let retData = res.data;
                if(retData.success == "success"){
                    this.items = retData.items
                    // console.log(this.items);
                }
            });
        }        
    }
};
</script>

<style scoped>
.sidebar {
    display: block;
    position: absolute;
    left: 0;
    top: 70px;
    bottom: 0;
    overflow-y: scroll;
}
.sidebar::-webkit-scrollbar {
    width: 0;
}
.sidebar-el-menu:not(.el-menu--collapse) {
    width: 250px;
}
.sidebar > ul {
    height: 100%;
}
</style>
