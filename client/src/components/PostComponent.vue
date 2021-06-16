
<template>
    <!--<h1>This is the Godil website :)</h1>-->
    <div class="container">
        <h1>Godil Posts</h1>
        <div class="create-post">
            <label for="create-post">Say Something...</label>
            <input type="text" id="create-post" v-model="text" placeholder="Create a post" />
            <button v-on:click="createPost">Post!</button>
        </div>
        <div><p> </p></div>
        <div class="login">
            <label id="loginLabel" for="enter-login">Login...</label>
            <input type="number" id="enter-login" v-model="textTwo" placeholder="Enter 3-digit pin" />
            <button v-on:click="login">Login!</button>
        </div>
        <hr />
        <p class="error" v-if="error">{{ error }}</p>
        <div class="posts-container">
            <div class="post"
                 v-for="(post, index) in posts.slice().reverse()"
                 v-bind:item="post"
                 v-bind:index="index"
                 v-bind:key="post._id"
                 v-on:dblclick="deletePost(post._id)">

                {{ `${post.createdAt.toString().slice(0,post.createdAt.toString().search("GMT")-4)}`}}
                <p class="text">{{ post.text }}</p>
            </div>
        </div>
    </div>
</template>
<script>
    import PostService from '../PostService';
    var posterName = "Anonymous";
    export default {
        name: 'PostComponent',
        data() {
            return {
                posts: [],
                error: '',
                text: ''
            }
        },
        async created() {
            try {
                this.posts = await PostService.getPosts();
            }
            catch (err) {
                console.log(err);
                this.error = err.message;
            }
        },
        methods: {
            async login() {
                if (!isNaN(this.textTwo)) {
                    const names = ['Isra', 'Harun', 'Asiya', 'Bilal', 'Fatima', 'Bushra', 'Asif'];
                    var pin = parseInt(this.textTwo,10);
                    var index = parseInt(pin / 100);
                    if (index <= 6 && (pin % 100) == index*3+7) {
                        posterName = names[index];
                        document.getElementById("loginLabel").innerHTML = "Logged in as: "+posterName;
                    }
                }
            },
            async createPost() {
                await PostService.insertPost(this.text+' - '+posterName);
                this.posts = await PostService.getPosts();
            },
            async deletePost(id) {
                await PostService.deletePost(id);
                this.posts = await PostService.getPosts();
            }
        }
    }
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    div.container {
        max-width: 800px;
        margin: 0 auto;
    }

    p.error {
        border: 1px solid #ff5b5f;
        background-color: #ffc5c1;
        padding: 10px;
        margin-bottom: 15px;
    }

    div.post {
        position: relative;
        border: 1px solid #5bd658;
        background-color: #bcffb8;
        padding: 10px 10px 30px 10px;
        margin-bottom: 15px;
    }

    div.created-at {
        position: absolute;
        top: 0;
        left: 0;
        padding: 5px 15px 5px 15px;
        background-color: darkgreen;
    }

    p.text {
        font-size: 22px;
        font-weight: 700;
        margin-bottom: 0;
    }
</style>
