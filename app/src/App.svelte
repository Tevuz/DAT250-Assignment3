<script lang="js">
    import {onMount} from "svelte";
    import {writable} from "svelte/store";

    export let page_shown = writable("/");
    onMount(() => $page_shown = window.location.pathname);
    window.addEventListener("popstate", () => $page_shown = window.location.pathname)

    import Router from "svelte-spa-router";

    import PollList from './lib/poll_list.svelte';
    import PollCreate from './lib/poll_create.svelte';

    let routes = {
        "/": PollList,
        "/polls/create": PollCreate
    }


</script>


<main>
    <Router {routes} />
</main>