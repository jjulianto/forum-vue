<template>
    <div class="container" style="width:100%">
        <div class="flex-grid">
            <div class="col-3 push-top">
                <ProfileCard v-if="!edit" :user="user" />
                <ProfileCardEditor v-else :user="user" />
            </div>

            <div class="col-7 push-top">
                <div class="profile-header">
                    <span class="text-lead"> {{ user.username }} recent activity </span>
                    <a href="#">See only started threads?</a>
                </div>
                <hr />
                <PostList :posts="user.posts" />
                <AppInfiniteScroll @load="fetchUserPosts" :done="user.posts.length === user.postsCount" />
            </div>
        </div>
    </div>
</template>
<script>
import PostList from '@/components/PostList'
import ProfileCard from '@/components/ProfileCard'
import ProfileCardEditor from '@/components/ProfileCardEditor'
import { mapGetters } from 'vuex'
import asyncDataStatus from '@/mixins/asyncDataStatus'

export default {
    components: {
        PostList,
        ProfileCard,
        ProfileCardEditor
    },
    mixins: [asyncDataStatus],
    props: {
        edit: {
            type: Boolean,
            default: false
        }
    },
    computed: {
        ...mapGetters('auth', { user: 'authUser' }),
        lastPostFetched() {
            if (this.user.posts.length === 0) return null
            return this.user.posts[this.user.posts.length - 1]
        }
    },
    methods: {
        fetchUserPosts() {
            return this.$store.dispatch('auth/fetchAuthUsersPosts', { startAfter: this.lastPostFetched })
        }
    },
    async created() {
        await this.fetchUserPosts()
        this.asyncDataStatus_fetched()
    }
}
</script>