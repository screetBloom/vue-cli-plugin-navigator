<template>
    <div id="app">
        <section class="section"><div id="logo"></div></section>
        <section class="section">
            <h1 class="h1">{{ title }}</h1>
        </section>
        <section class="section">
            <v-input
                v-model="userInput"
                placeholder="Search for the page you want to open..."
                center
            />
        </section>
        <section class="section">
            <v-table :data="filterPages" @row-click="rowClickHandler">
                <v-table-col label="Title" prop="title" />
                <v-table-col label="Tags" prop="type" default="none">
                    <template slot-scope="props">
                        <span
                            class="tag"
                            v-for="tagName of props.row.tags"
                            :key="tagName"
                            :style="{ backgroundColor: getTagColor(tagName) }"
                            >{{ tagName }}</span
                        >
                    </template>
                </v-table-col>
                <v-table-col label="More information" default="none">
                    <template slot-scope="props">
                        <a
                            class="link"
                            href="javascript:;"
                            @click.stop="open(props.row);"
                            >more...</a
                        >
                    </template>
                </v-table-col>
            </v-table>
        </section>
        <section class="section">
            Tip: Clicking on any row in the table will open the page.
        </section>
        <footer class="footer">@ create by vue-cli-plugin-navigator</footer>
        <page-detail-view
            :tags="tags"
            :visible.sync="showDetail"
            :page-config="showPageDetail"
        />
    </div>
</template>

<style lang="scss">
html {
    height: 100%;
}
body {
    margin: 0;
    padding: 0;
    height: 100%;
}
#app {
    position: relative;
    font-family: sans-serif;
    box-sizing: border-box;
    color: #2c3e50;
    margin-left: auto;
    margin-right: auto;
    padding: 1px 10px 42px;
    min-width: 320px; // 移动端最小屏幕
    max-width: 1200px; // 最大尺寸
    min-height: 100%;
    -webkit-overflow-scrolling: touch;
}
.tag {
    display: inline-block;
    border-radius: 10px;
    margin-right: 5px;
    padding: 2px 5px;
    color: #ffffff;
    font-size: 12px;
    flex: 0 0 auto;
}
@media screen and (max-width: 544px) {
    .v-table__col:nth-child(2) {
        display: none;
    }
}
</style>
<style lang="scss" scoped>
@import '../../assets/color';
@import '../../assets/img';

.section {
    margin: 20px 0;
}

.h1 {
    text-align: center;
}

#logo {
    margin: 0 auto;
    width: 150px;
    height: 150px;
    background: url($logo-img);
    background-size: cover;
}

.footer {
    position: absolute;
    left: 0;
    bottom: 0;
    padding: 10px 0;
    width: 100%;
    text-align: center;
}

.link {
    color: $bright;
    text-decoration: none;
}

.v-table__row:hover {
    .link {
        color: #ffffff;
    }
}
</style>

<script lang="ts">
/* eslint-disable prefer-destructuring */
import Vue from 'vue';
import VInput from '../../ui/input/index';
import { VTable, VTableCol } from '../../ui/table/index';
import getConfig from './getConfig';
import PageDetailView from './page.detail.vue';

export default Vue.extend({
    name: 'app',
    components: {
        VInput,
        VTable,
        VTableCol,
        PageDetailView
    },
    data() {
        const pluginConfig: PluginConfig = getConfig();
        return {
            userInput: '',
            title: pluginConfig.title,
            tags: pluginConfig.defineTags,
            pages: pluginConfig.pages,
            showDetail: false,
            showPageDetail: {
                title: '',
                tags: [],
                description: ''
            }
        };
    },
    computed: {
        filterPages(): PageConfig[] {
            return this.pages.filter((config: PageConfig) => {
                return new RegExp(this.userInput, 'ig').test(config.title);
            });
        }
    },
    methods: {
        rowClickHandler(row: PageConfig) {
            location.href = row.path;
        },
        open(pageConfig: PageConfig) {
            //@ts-ignore
            this.showPageDetail = pageConfig;
            this.showDetail = true;
        },
        getTagColor(tagName: string) {
            const tag = this.tags.find(tag => tag.name === tagName);
            return tag ? tag.color : '#2e4053';
        }
    }
});
</script>
