<script lang="ts">
    import { PUBLIC_AEC_APP_ID } from '$env/static/public';
    import { type APSAuthClient, type AutodeskUserInformation, createAPSAuthClient } from '$lib'
    import { setContextAPSAuthClient } from '$lib/context'
 
    const { children } = $props();

    let is_authorized = $state<boolean>(false)
    let user_info = $state<AutodeskUserInformation|null>(null);

    const client: APSAuthClient = createAPSAuthClient({
        client_id: PUBLIC_AEC_APP_ID,
        scope: ["openid", "data:read"]
    })

    client.subscribe(async access_token => {
        is_authorized = client.isAuthorized()
        user_info = await client.getUserInfo()
    })

    setContextAPSAuthClient(client)

    async function login() {
        const redirect_uri = `${window.location.origin}/auth/autodesk/callback`
        const state = window.location.href
        await client.loginWithRedirect({ redirect_uri, state })
    }

    const logout = async () => await client.logout()

    const navigations = [{
        href: "/",
        name: "Home"
    }, {
        href: "/first",
        name: "First"
    }, {
        href: "/second",
        name: "Second"
    }] as const

</script>

<div class="top-bar">
    <div class="navigation-bar">
        {#each navigations as {href, name}}
            <a {href}> {name} </a>
        {/each}
    </div>

    <div> APS | Authentication Client for Single Page Applications </div>
    
    <div class="navigation-bar">
        {#if user_info}
            <div> 
                Hello {user_info.name}
                <img src={user_info.picture} alt="user-profile" class="user-profile">
            </div>
        {/if}
        <button disabled={is_authorized} onclick={login}> Login </button>
        <button disabled={!is_authorized} onclick={logout}> Logout </button>
    </div>
</div>

<div>
    {@render children()}
</div>

<style>
    .top-bar {
        display: flex;
        justify-content: space-between;
        padding: 0.50rem;
        border-bottom: 0.1rem solid black 
    }

    .navigation-bar {
        display: flex;
        gap: 0.50rem;
    }

    .user-profile {
        width: 1rem;
    }
</style>