title: vue-scroller
date: 2018-05-17 09:09:01
tags: ['vue-scroller']
categories: ['vue-scroller']
---

最近用vue写移动端开发，导致很多页面的展示需要用到下拉分页，所以将自己用到的知识点进行总结一下.

## Quick Start 

### 安装 vue-scorller

``` bash
$ npm install vue-scroller -d
```


### 引用main.js

``` bash
$ import VueScroller from 'vue-scroller'
$ import Vue from 'vue'
$ Vue.use(VueScroller)
```

### HTML模板

``` bash
<template>
    <v-layout row class="activity">
        <v-flex xs12 sm12 md12>
            <v-card>
                <v-list two-line>
                    <scroller :on-refresh="refresh" :on-infinite="infinite" ref="scrolling">
                        <div v-for="(item, index) in items" class="row" :class="{'grey-bg': index % 2 == 0}">
                            <v-list-tile avatar :key="item.title" @click="toggle(item)">
                                <v-list-tile-avatar>
                                    <img :src="item.logo">
                                </v-list-tile-avatar>
                              <v-list-tile-content>
                                    <v-list-tile-title>{{ item.title }}</v-list-tile-title>
                                    <v-list-tile-sub-title>{{item.address}}</v-list-tile-sub-title>
                                    <v-list-tile-sub-title>{{item.startdate}} - {{item.enddate}}</v-list-tile-sub-title>
                              </v-list-tile-content>
                            </v-list-tile>
                        </div>
                    </scroller>
                </v-list>
            </v-card>
        </v-flex>
    </v-layout>
</template>
```
* on-refresh 是一开始加载的时候下拉刷新的时候请求数据
* on-infinite 是每当向上滑动的时候去请求数据

### JS

``` bash
<script>
    import crypto from "crypto";

    export default {
        data: function () {
            return {
                items: [],
                province: '',
                city: '',
                src: 'inveno',
                sge: '2017-12-12',
                sle: '2018-05-08',
                pi: 1,
                ps: 10,
                count: 0,
                noData: '',
                pagecount: 0
            }
        },

        mounted() {
            let self = this;

            self.noData = '';
            let pagination = {
                pi: self.pi,
                ps: self.ps
            };
            self.initialize(pagination);
            self.top = 1;
            self.bottom = 10;
        },

        created () {
            let self = this;
            self.items = [];
            self.$store.commit('changeTitle', '查活动');
        },

        methods: {
            queryActivity(params) {
                let self = this;

                return new Promise((resolve, reject) => {
                    self.$request.post(self.$store.state.queryActivity, params)
                        .then((response) => {
                            if (response.status == '200' && response.data.code == '200') {
                                resolve(response.data.content);
                            } else {
                                reject(response.data.msg)
                            }
                        }).catch((error) => {
                            reject(error);
                        });
                });
            },

            initialize(pagination) {
                let self = this;
                let params = {
                    src: 'inveno',
                    province: '北京',
                    city: '朝阳',
                    sge: '2017-12-12',
                    sle: '2018-05-08'
                };
                let optionList = [];

                self.queryActivity(Object.assign(params, pagination))
                    .then((result) => {
                        let data = result.list;
                        let list = [];

                        data.forEach((item) => {
                            let startdate = self.$utils.formatDate(new Date(item.startdate), 'yyyy-MM-dd hh:mm:ss');
                            self.$set(item, 'startdate', startdate);

                            let enddate = self.$utils.formatDate(new Date(item.enddate), 'yyyy-MM-dd hh:mm:ss');
                            self.$set(item, 'enddate', enddate);

                            list.push(item);
                        });
                        self.items = self.items.concat(list);
                        self.pagecount = result.pagecount;
                        self.count = Math.ceil(result.pagecount / self.ps);
                    }).catch(error => {
                        let info = {
                            alertInfo: error ? error.toString() : '',
                            color: 'error'
                        };
                        self.$Snackbar(info);
                    });
            },

            refresh(done) {
                let self = this;

                setTimeout(function () {
                    done(true);
                    console.log('refresh')
                }, 1500);
            },

            infinite(done) {
                let self = this;

                if(this.noData) {
                    setTimeout(()=>{
                        this.$refs.scrolling.finishInfinite(2);
                    });
                    return false;
                }

                if(Math.ceil(self.bottom / self.ps) > self.count) {
                    setTimeout(() => {
                        done(true)
                    }, 1500)
                    return false;
                }

                setTimeout(function () {
                    var start = self.bottom + 1;
                    let pagination = {
                        pi: start,
                        ps: 10
                    };
                    self.initialize(pagination);

                    self.bottom = self.bottom + 10;
                    setTimeout(() => {
                        if(start > self.pagecount - self.ps) {
                            self.noData = "没有更多数据";
                        }
                        self.$refs.scrolling.resize();
                        done();
                    });
                }, 1500);
            },

            toggle(item) {
                let self = this;

                if(item.url && !!item.url) {
                    window.open(item.url);
                }
            }
        }
    }
</script>
```

### CSS

```bash
<style lang="scss" scoped>
    @import '../../assets/css/color.scss';

    .activity {
        .row {
            width: 100%;
            height: 90px;
            padding: 10px 0;
            font-size: 16px;
            line-height: 30px;
            text-align: center;
            color: #444;
            background-color: #fff;
        }

        .grey-bg {
            background-color: #eee;
        }
    }
</style>
```

More info: [vue-scroller](https://github.com/wangdahoo/vue-scroller)
