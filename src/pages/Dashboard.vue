<template>
    <div class="container-fluid">
        <div class="row">
            <div class="col-12 col-md-5 col-xl-4">
                <div v-if="! $root.isMobile">
                    <router-link to="/add" class="btn btn-primary mb-3"><font-awesome-icon icon="plus" /> Add New Monitor</router-link>
                </div>

                <div v-if="showList" class="shadow-box list mb-4">
                    <div v-if="Object.keys($root.monitorList).length === 0" class="text-center mt-3">
                        No Monitors, please <router-link to="/add">add one</router-link>.
                    </div>

                    <router-link v-for="(item, index) in sortedMonitorList" :key="index" :to="monitorURL(item.id)" class="item" :class="{ 'disabled': ! item.active }" @click="$root.cancelActiveList">
                        <div class="row">
                            <div class="col-6 col-md-8 small-padding" v-bind:class="{ 'monitorItem': $root.userHeartbeatBar == 'bottom' || $root.userHeartbeatBar == 'none' }">
                                <div class="info">
                                    <Uptime :monitor="item" type="24" :pill="true" />
                                    {{ item.name }}
                                </div>
                            </div>
                            <div class="col-6 col-md-4" v-show="$root.userHeartbeatBar == 'normal'" :key="$root.userHeartbeatBar">
                                <HeartbeatBar size="small" :monitor-id="item.id" />
                            </div>
                        </div>

                        <div class="row" v-if="$root.userHeartbeatBar == 'bottom'">
                            <div class="col-12">
                                <HeartbeatBar size="small" :monitor-id="item.id" />
                            </div>
                        </div>
                    </router-link>
                    
                </div>
            </div>
            <div class="col-12 col-md-7 col-xl-8">
                <router-view />
            </div>
        </div>
    </div>
</template>

<script>

import HeartbeatBar from "../components/HeartbeatBar.vue";
import Uptime from "../components/Uptime.vue";

export default {
    components: {
        Uptime,
        HeartbeatBar,
    },
    data() {
        return {}
    },
    computed: {
        sortedMonitorList() {
            let result = Object.values(this.$root.monitorList);

            result.sort((m1, m2) => {

                if (m1.active !== m2.active) {
                    if (m1.active === 0) {
                        return 1;
                    }

                    if (m2.active === 0) {
                        return -1;
                    }
                }

                if (m1.weight !== m2.weight) {
                    if (m1.weight > m2.weight) {
                        return -1;
                    }

                    if (m1.weight < m2.weight) {
                        return 1;
                    }
                }

                return m1.name.localeCompare(m2.name);
            })

            return result;
        },
        showList() {
            return ! this.$root.isMobile || this.$root.showListMobile;
        },
    },
    methods: {
        monitorURL(id) {
            return "/dashboard/" + id;
        },
    },
}
</script>

<style lang="scss" scoped>
@import "../assets/vars.scss";

.container-fluid {
    width: 98%
}

.list {
    height: auto;
    min-height: calc(100vh - 240px);

    .item {
        display: block;
        text-decoration: none;
        padding: 15px 15px 12px 15px;
        border-radius: 10px;
        transition: all ease-in-out 0.15s;

        &.disabled {
            opacity: 0.3;
        }

        .info {
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        &:hover {
            background-color: $highlight-white;
        }

        &.active {
            background-color: #cdf8f4;
        }
    }
}

.small-padding {
    padding-left: 5px !important;
    padding-right: 5px !important;
}

.dark {
    .list {
        .item {
            &:hover {
                background-color: $dark-bg2;
            }

            &.active {
                background-color: $dark-bg2;
            }
        }
    }
}

.monitorItem {
    width: 100%;
}

</style>
