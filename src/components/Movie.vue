<template>
    <Layout :has_share="false" title="正在热映">
        <div class="page_wrap">
            <div class="page_bd">
                <div class="content">
                    <mu-refresh-control :refreshing="refreshing" :trigger="trigger" @refresh="refresh"/>
                    <div class="list">
                        <MediaCard :media="films"></MediaCard>
                    </div>
                    <mu-infinite-scroll :scroller="scroller" :loading="getLoading" @load="loadMore"/>
                </div>
            </div>
        </div>
    </Layout>
</template>
<script>
let _self;
import Layout from '@/components/Layout';
import MediaCard from '@/components/MediaCard';

export default {
    data: function() {
        return {
            loading: 'init',
            films: [],
            page: 1,
            refreshing: false,
            scroller: null,
            trigger: null,
        };
    },
    created() {
        _self = this;
        this.getLocationMovies();
    },
    activated() {
        this.trigger = this.$el;
        this.scroller = this.$el; // 滚动的元素
    },
    deactivated() {
        this.trigger = null;
        this.scroller = null;
    },
    methods: {
        getLocationMovies() {
            let params = {};
            params.ts = '201851015581118117';
            params.locationId = this.city.id;
            this.loading = 'loading';
            this.$store.dispatch('getLocationMovies', params).then((response) => {
                let res = response.data;
                if (response.ok && response.status === 200) {
                    this.loading = 'loaded';
                    for(let item of res.ms) {
                        item.title = item.tCn;
                        item.image = item.img;
                    }
                    this.films = res.ms
                    
                } else {
                    this.loading = 'error';
                }

            }).catch(function(err) {
                console.error(err);
            });
        },
        refresh() {
            this.page = 1;
            this.getLocationMovies();
        },
        loadMore() {
            this.page++;
            this.getLocationMovies();
        },
    },
    computed: {
        getLoading() {
            if (this.loading === 'loading') {
                return true;
            } else {
                return false;
            }
        },
        city() {
          return this.$store.state.user.city;
        },
    },
    components: {
        Layout,
        MediaCard,
    }
};

</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
@import url('../assets/less/common.less');
.page_wrap {
    width: 100%;
    height: 100%;
    background: #f5f5f5;
    .page_hd {}
    .page_bd {
        padding-top: @nav_bar/3;
        .content {
            position: relative;
            .list {
                text-align: center;
            }
        }
    }
}
</style>