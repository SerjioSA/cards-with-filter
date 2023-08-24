<script setup>
const Posts = ref([]) 
const currentPosts = ref([]) 
const currentUsers = ref([]) 
const searchString = ref("") 

async function getPosts() {
    await useNuxtApp().$api.get("https://jsonplaceholder.typicode.com/posts/")
    .then((response) => {
        currentPosts.value = response.data;
        useNuxtApp().$api.get("https://jsonplaceholder.typicode.com/users/")
            .then((response) => {
                
                Posts.value = []
                currentUsers.value = response.data;
                
                const numPosts = currentPosts.value.length;
                const numUsers = currentUsers.value.length;

                for (let i = 0; i < numPosts; i++) {
                    for (let j = 0; j < numUsers; j++) {
                          if (currentPosts.value[i]["userId"] === currentUsers.value[j]["id"]){
                            Posts.value.push({
                                userId: currentPosts.value[i]["userId"],
                                title: currentPosts.value[i]["title"],
                                body: currentPosts.value[i]["body"],
                                username: currentUsers.value[j]["name"],
                            })
                          }
                    }
                }
                if (searchString.value.length > 0) {
                    Posts.value = Posts.value.filter(post => post.username.toLowerCase().startsWith(searchString.value.toLowerCase()));   
                }
            })
    })
}
onMounted(() => {
    getPosts()
});


</script>



<template>
<div>
    <q-input class="md:w-1/2 md:mx-auto min-[300px]:w-full min-[300px]:px-5 "
        outlined
        v-model="searchString"
        label="Search by author"
        @update:model-value="getPosts()"
        :debounce="200"
        >
        <template v-slot:prepend>
          <q-icon name="search" />
        </template>
    </q-input>
    
    <div class="drop-shadow-2xl flex  justify-center items-center h-screen grid lg:grid-cols-3 md:grid-cols-1 gap-10 md:p-10 min-[300px]:p-5">
        
        <div v-for="item in Posts" :key="item.id" class="block p-6 bg-white border border-gray-200 rounded-lg shadow hover:bg-gray-100 dark:bg-gray-800 dark:border-gray-700 dark:hover:bg-gray-700">
            <h5 class="mb-2 text-2xl font-bold tracking-tight text-blue-500 dark:text-white">{{item.title}}</h5>
            <p class="font-normal text-gray-700 dark:text-gray-400">{{ item.body }}</p>
            <p class="font-normal text-gray-500 dark:text-gray-400 mt-6">{{ item.username }}</p>
        </div>
        
    </div>
</div>
</template>
