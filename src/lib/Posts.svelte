<script>
    import { onMount, onDestroy } from "svelte";
    import { currentUser, pb } from "./pocketbase";
  import { prevent_default } from "svelte/internal";

    let posts = [];
    let title;
    let content;
    let unsubscribe;

    onMount(async () => {
        const postsList = await pb.collection('threads')
            .getList(1, 50, { 
                sort: '-created', expand: 'user'
            });

        posts = postsList.items

        unsubscribe = await pb.collection('threads')
            .subscribe('*', async({action, record}) => {
                if (action == 'create') {
                    const user = await pb.collection('users').getOne(record.user);
                    record.expand = {user}
                    posts = [record, ...posts]
                }
                if (action == 'delete') {
                    posts = posts.filter((post) => {
                        return post.id !== record.id
                    });
                }
            });
    });

    onDestroy(() => {
        unsubscribe?.();
    });

    async function createPost() {
        const data = {
            title,
            content,
            user: $currentUser.id
        };

        const createdPost = await pb.collection('threads').create(data)
        title = ""
        content = ""
    }
</script>

<div class="posts">
    {#each posts as post (post.id)}
        <div class="single-post">
            <h3>{post.title}</h3>
            <p>{post.content}</p>
            <p class="post-author">{post.expand?.user?.username}</p>
        </div>
    {/each}
</div>

<div class="form-container">
{#if $currentUser}
        <form on:submit|preventDefault={createPost}>
            <input type="text" placeholder="Title" bind:value={title}>
            <textarea cols="30" rows="10" placeholder="Content" bind:value={content} />  
            <button>Create Post</button>
        </form>
{:else}
    <h3 class="sticky">Login to create a post</h3>
{/if}
</div>

<style>
    .sticky {
        position: sticky;
        top: 97px;
        text-align: center;
    }

    .posts {
        display: flex;
        flex-direction: column;
        width: 65%;
    }
    
    .single-post {
        padding: 20px;
        border-bottom: 1px solid rgb(230, 230, 230);
    }

    .post-author {
        font-style: italic;
        text-align: end;
    }

    .form-container {
        display: flex;
        flex-direction: column;
        flex-grow: 1;
        background-color: rgba(230, 230, 230, 1);
        padding: 20px;
    }
    
    form {
        display: flex;
        flex-direction: column;
        gap: 10px;
        position: sticky;
        height: max-content;
        top: 97px;
    }
    
    form input, form textarea {
        font-size: 16px;
        width: 100%;
        font-family: Arial, Helvetica, sans-serif;
        resize: none;
        padding: 10px;
        background-color: white;
        border: none;
    }

    form button {
        background-color: #66a5aa;
        border: none;
        font-size: 16px;
        padding: 10px;
        color: white;
        cursor: pointer;
        transition: background-color 0.2s ease-in;
    }

    form button:hover {
        background-color: #90dbe1;
    }
</style>