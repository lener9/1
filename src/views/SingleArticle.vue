<template>
    <div>
        <div id="page" @scroll="scroll">
            <div class="content">
                <div class="article">
                    <button v-on:click="pop">返回</button>
                    <div class="markdown" v-html="md.render(article)"></div>
                </div>
            </div>
        </div>
    </div>
</template>

<script lang="ts">
    import { Component, Vue } from "vue-property-decorator";
    import { Action } from 'vuex-class';
    import MarkdownIt from 'markdown-it';
    const namespace: string = 'article_store';

    @Component({
        components: { }
    })
    export default class SingleArticle extends Vue {

        article: string = "";

        md = new MarkdownIt();

        @Action("saveScrollTop")
        saveScrollTop!: Function;

        @Action("viewArticleDetail", {namespace})
        viewArticleDetail!: Function;

        async mounted() {
            let category = this.$route.params["category"];
            let technology = this.$route.params["technology"];
            let filename = this.$route.params["filename"];
            this.article = await this.viewArticleDetail({
                category: category,
                technology: technology,
                filename: filename
            });
        }

        scroll() {
            const page = document.querySelector("#page");
            if (page === null) return;
            this.saveScrollTop(page.scrollTop);
        }

        pop() {
            this.$router.go(-1);
        }
    }
</script>

<style scoped lang="less">
    #page {
        width: 100%;
        height: 100%;
        overflow-y: scroll;
        touch-action: pan-y;
        -webkit-overflow-scrolling: touch;
        z-index: 100;
    }

    .content {
        padding-top: 120px;
        color: #fff;
        text-align: left;
        width: 100%;
    }

    .article {
        margin-left: 100px;
        margin-right: 100px;
        background-color: rgba(20, 20, 20, 0.4);
        border-radius: 8px;
        padding: 24px;
    }

    button {
        box-shadow: none;
        outline: none;
        font-size: 14px;
        cursor: pointer;
        padding: 0 16px 0 16px;
        background-color: transparent;
        border-width: 1px;
        border-style: solid;
        border-radius: 4px;
        border-color: #fff;
        color: #fff;
        height: 32px;
        margin-right: 12px;
        margin-bottom: 20px;
    }

    button:hover {
        animation: button-bg-color 600ms 1;
        animation-fill-mode : forwards
    }

    @keyframes button-bg-color {
        0% {
            background-color: transparent;
        }
        100% {
            background-color: #fff;
            color: #2c3e50;
        }
    }

    ::v-deep .markdown pre {
        border-radius: 4px;
        padding: 12px;
        background-color: rgba(44, 62, 80, 0.4);
    }

    ::v-deep .markdown code {
        color: #17b5d2;
        font-weight: bold;
    }

    ::v-deep .markdown pre code {
        color: #ddd;
        font-weight: normal;
    }

    ::v-deep .markdown li {
        font-size: 14px;
        color: #ddd;
    }
</style>
