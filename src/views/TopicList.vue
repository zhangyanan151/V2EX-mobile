<template>
    <div>
        <List :list-data="currentTopicList"></List>
    </div>
</template>
<script>
    import { mapGetters, mapActions } from 'vuex';
    import List from 'components/List';
    export default {
        name: 'TopicList',
        components: {
            List
        },
        methods: {
            ...mapActions(['getListByTagIfNoCache', 'getListByNodeIfNoCache', 'showLoading', 'hideLoading']),
            fetchDataByTag(tag) {
                this.$store.dispatch('showLoading');
                Promise.all([
                    this.$store.dispatch('getListByTagIfNoCache', tag)
                ])
                .then(() => {
                    this.$store.dispatch('hideLoading');
                });
            },
            fetchDataByNode(node) {
                this.$store.dispatch('showLoading');
                Promise.all([
                    this.$store.dispatch('getListByNodeIfNoCache', node)
                ])
                .then(() => {
                    this.$store.dispatch('hideLoading');
                });
            }
        },
        computed: {
            currentTag() {
                return this.$route.query.key ? this.$route.query.key : null;
            },
            currentNode() {
                if (!this.$route.query.node) {
                    return {
                        node_id: 0,
                        node_name: null
                    };
                };
                const node = this.$route.query.node.split('!');
                return {
                    node_id: node[0],
                    node_name: node[1]
                };
            },
            currentTopicList() {
                return this.currentTag ? this.topicList[this.currentTag] : this.topicList[this.currentNode.node_name];
            },
            ...mapGetters(['topicList'])
        },
        watch: {
            '$route'(route) {
                if (route.query.node) {
                    this.fetchDataByNode(this.currentNode);
                } else {
                    this.fetchDataByTag(this.currentTag);
                }
            }
        },
        created() {
            if (this.$route.query.node) {
                this.fetchDataByNode(this.currentNode);
            } else {
                this.fetchDataByTag(this.currentTag);
            }
        }
    };
</script>
