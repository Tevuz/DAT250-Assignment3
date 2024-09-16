<script>
    import {onMount} from "svelte";
    import {push} from "svelte-spa-router";

    let app_indices = [];
    let app_content = {};
    let app_data = {};

    load()
    onMount(() => {
        const intervalId = setInterval(load, 5000)
        return () => clearInterval(intervalId);
    })

    function load() {
        fetch("/api/polls")
            .then((response) => response.json())
            .then((content) => {
                for (const poll of content) {
                    if (!app_content[poll.id]) {
                        app_indices = [...app_indices, poll.id];
                        app_content[poll.id] = poll;
                        app_data[poll.id] = {};
                    } else if (app_content[poll.id] != poll) {
                        app_content[poll.id] = poll;
                    }
                }
            })
    }

    let formdata = {};

    function handleKeypress(event) {
        if (event.code === "Enter")
            this.checked = !this.checked;
    }

    function handleCheckboxChange(id, event) {
        formdata[id] = event.target.checked;
        console.log(formdata)
    }

    function handleVote(poll, event) {
        app_data[poll.id].voted = true;
        app_data[poll.id].options = [];
        for (const id of poll.voteOptions.map(option => option.id).filter((id) => formdata[id])) {
            app_data[poll.id].options.push(id);

            let body = JSON.stringify({
                "username": "unknown",
                "voteOption": { id }
            });

            console.log(body);

            fetch("/api/votes", {
                method: "POST",
                body,
                headers: { "Content-Type": "application/json" }
            })
        }

        setTimeout(() => fetch("/api/polls/" + poll.id)
            .then((response) => response.json())
            .then((poll) => {
                if (!app_content[poll.id]) {
                    app_indices = [...app_indices, poll.id];
                    app_content[poll.id] = poll;
                    app_data[poll.id] = {};
                } else if (app_content[poll.id] != poll) {
                    app_content[poll.id] = poll;
                }
            }), 50);
    }

    function handleCreate() {
        push("/polls/create");
    }
 </script>

<div class="content">
    <div class="row">
        <h1 class="column">Polls</h1>

        <span class="column align-right">
            <button class="button button-accent"
                    type="button"
                    on:click|preventDefault={handleCreate}>
                <h2>Create Poll</h2>
            </button>
        </span>
    </div>

    {#each app_indices as index}
        {#await app_content[index]}
            Loading Poll...
        {:then poll}
            <div class="entry">
                <h2>{poll.question}</h2>
                {#if app_data[index].voted}
                    {@const total = poll.voteOptions.reduce((acc, option) => acc + option.votes.length, 0)}
                    <div class="select">
                        <form on:submit|preventDefault={(event) => handleVote(poll, event)}>
                            {#each poll.voteOptions as option}
                                {@const progress = option.votes.length*100 / total}
                                <label class="item" style="
                                    background-image: linear-gradient(to right, var(--col-accent) {progress}%, white {progress}%);">
                                    {#if app_data[index].options.includes(option.id)}
                                        <span class="icon selected"/><span>{option.caption}</span>
                                    {:else}
                                        <span class="icon"/><span>{option.caption}</span>
                                    {/if}
                                    <span class="float-right">{option.votes.length} votes</span>
                                    <input disabled />
                                </label>
                            {/each}
                            <div class="align-right">
                                <span>{total} votes</span>
                                <button class="button"
                                        disabled>
                                    <h3>Voted</h3>
                                </button>
                            </div>
                        </form>
                    </div>
                {:else}
                    <div class="select">
                        <form on:submit|preventDefault={(event) => handleVote(poll, event)}>
                            {#each poll.voteOptions as option}
                                <label class="item">
                                    <input type="checkbox"
                                           on:keypress={handleKeypress}
                                           on:change={(event) => handleCheckboxChange(option.id, event)}
                                    /><span class="icon"/><span>{option.caption}</span>
                                </label>
                            {/each}
                            <div class="align-right">
                                <span>{poll.voteOptions.reduce((acc, option) => acc + option.votes.length, 0)} votes</span>
                                <button class="button button-accent"
                                        type="submit">
                                    <h3>Vote</h3>
                                </button>
                            </div>
                        </form>
                    </div>
                {/if}
            </div>
        {:catch error}
            {error}
        {/await}
    {/each}
</div>

