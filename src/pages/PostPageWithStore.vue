<template>
    <div>
        
       <h1>Страница с заметками</h1>
        <!-- <my-input
            v-focus
            v-model="searchQuery"
            placeholder = "Поиск..."
        >

        </my-input> -->
        <div class="app__btns">
           <my-button
                @click ="showDialog"
                style="margin: 10px 0;"
            >
                Создать заметку
            </my-button>
            <!-- <my-select
                v-model = "selectedSort"
                :options = "sortOptions"
            />  -->
        </div>
        <my-dialog v-model:show = "dialogVisible">
            <post-form 
                @create = "createPost"
            />  
        </my-dialog>
        
        <post-list
             :posts="sortedAndSearchedPosts"
             @remove = "removePost"
             v-if = "!isPostsLoading"
        />
        <div v-else>
            Идёт загрузка...
        </div>
        <div v-intersection="loadMorePosts" class="observer"> </div> 
        <div class="page__wrapper">
            <div 
                v-for="pageNumber in totalPages" 
                :key="pageNumber"
                class="page"
                :class="{
                    'current-page': page === pageNumber
                }"
                @click="changePage(pageNumber)"
            >
                {{ pageNumber }}
            </div>
        </div>
    </div>

</template>

<script>
import PostForm from '@/components/PostForm'
import PostList from '@/components/PostList'
import MyButton from '@/components/UI/MyButton'
import MySelect from '@/components/UI/MySelect'
import MyInput from '@/components/UI/MyInput'
import axios from 'axios'
import {mapActions, mapGetters, mapMutations, mapState} from 'vuex'

export default{
    components: {
        MyInput, PostList, PostForm, MyButton, MySelect
    },
    data() {
        return{
            dialogVisible: false,
        }
    },
    methods:{
        ...mapMutations({
            setPage: 'post/setPage'
        }),
        ...mapActions({
           loadMorePosts: 'post/loadMorePosts' ,
           fetchPosts: 'post/fetchPosts'
        }),
        createPost(post){
            this.posts.push(post);
            this.dialogVisible = false;
        },
        removePost(post) {
            this.posts = this.posts.filter(p => p.id !== post.id)
        },
        showDialog(){
            this.dialogVisible = true;
        }, 
        //changePage(pageNumber) {
        //    this.page = pageNumber  
        //},
        
    },

    mounted() {
        this.fetchPosts();
    }, 
    computed: {
        ...mapState({
            posts: state => state.post.posts,
            isPostsLoading: state => state.post.isPostsLoading,
            selectedSort: state => state.post.selectedSort,
            searchQuery: state => state.post.searchQuery,
            page: state => state.post.page,
            limit: state => state.post.limit,
            totalPages: state => state.post.totalPages,
            sortOptions: state => state.post.sortOptions,
        }),
        ...mapGetters({
            sortedPosts: 'post/sortedPosts' ,
            sortedAndSearchedPosts: 'post/sortedAndSearchedPosts'
        })
    },
    watch: {
        // page() {
        //     this.fetchPosts()
        // }
    }
   
}
</script>

<style>

.app__btns{
    margin: 15px 0;
    display: flex;
    justify-content: space-between;
}
.page__wrapper{
    display: flex;
    margin-top: 15px;
}
.page{
    border: 1px solid black;
    padding: 10px;
}
.current-page{
    border: 2px solid teal;
}
.observer{
    height: 30px;
    background: green;
}

</style>