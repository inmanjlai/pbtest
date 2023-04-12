<script>
    import {currentUser, pb} from "./pocketbase"

    let username;
    let password;

    async function login() {
        await pb.collection('users').authWithPassword(username, password)
    }

    async function signUp() {

        try {
            const data = {
                username,
                password,
                passwordConfirm: password
            };
    
            const createdUser = await pb.collection('users').create(data);
            await login();
        } catch (err) {
            console.error(err);
        }
    }

    function signOut() {
        pb.authStore.clear();
    }


</script>

{#if $currentUser}
    <div class="user-controls">
        <p>Signed in as {$currentUser.username}</p>
        <p>
            <button on:click={signOut}>Sign Out</button>
        </p>
    </div>
{:else}
    <form on:submit|preventDefault>
        <input
            placeholder="Username" 
            type="text" 
            bind:value={username}
        />
        <input
            placeholder="Password" 
            type="password" 
            bind:value={password}
        />
        <button on:click={login}>Login</button>
        <button on:click={signUp}>Sign Up</button>
    </form>
{/if}

<style>
    form, .user-controls {
        display: flex;
        align-items: center;
        gap: 20px;
    }

    input {
        background-color: rgba(230, 230, 230, 1);
        padding: 10px;
        border: none;
    }

    button {
        background-color: #66a5aa;
        border: none;
        padding: 10px;
        color: white;
        cursor: pointer;
        transition: background-color 0.2s ease-in;
    }

    button:hover {
        background-color: #90dbe1;
    }
</style>