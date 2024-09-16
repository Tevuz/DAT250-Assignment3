<script>
    import {pop} from "svelte-spa-router";

    let question = "";
    let questionValid = true;
    let options = [""];
    let optionsValid = true;

    function handleInput(index, event) {
        options[index] = event.target.value.trim();

        if (index === options.length - 1 && event.target.value !== "") {
            options = [...options, ""];
        }

        optionsValid = true;
    }

    function handleChange(index, event) {
        event.target.value = event.target.value.trim()
        if (index !== options.length - 1 && event.target.value === "") {
            options.splice(index, 1);
            options = options;
        }

        optionsValid = true;
    }

    function handleDelete(index) {
        if (options.length === 1) {
            options[0] = "";
        } else {
            options.splice(index, 1);
        }

        optionsValid = true;
        options = options;
    }

    function handleCancel() {
        pop();
    }

    function handleSubmit() {
        questionValid = question.length > 0;
        optionsValid = options.length > 1;

        if (!questionValid || !optionsValid)
            return;

        const body = JSON.stringify({
            "question": question,
            "author_username": "unknown",
            "voteOptions": options.slice(0,-1).map((caption) => ({ "caption": caption }))});
        console.log(body);

        // submit
        fetch("/api/polls", {
            method: "POST",
            body: body,
            headers: { "Content-Type": "application/json" }
        });

        pop();
    }

</script>

<div class="form">
    <h1>Create Poll</h1>

    <h2>Question</h2>

    <input id="question"
           type="text"
           placeholder="Question"
           on:input={(event) => {question = event.target.value.trim(); questionValid=true; }}>

    {#if !questionValid}
        <p class="errorMessage"> * Question is required.</p>
    {/if}

    <h2 class="label">Options</h2>

    {#each options as option, index}
        <div class="combobox">

            <input id="option_{index}"
                   type="text"
                   value={option}
                   placeholder="Option {index + 1}"
                   on:input={(event) => handleInput(index, event)}
                   on:change={(event) => handleChange(index, event)}>

            {#if option}
                <button class="button-delete"
                        type="button"
                        on:click={() => handleDelete(index)}>
                    &#x2573
                </button>
            {/if}

        </div>
    {/each}

    {#if !optionsValid}
        <p class="errorMessage"> * At least one option is required.</p>
    {/if}

    <div class="float-right">
        <button class="button button-cancel"
                type="button"
                on:click={handleCancel}>
            <h2>Cancel</h2>
        </button>

        <button class="button button-accent"
                type="button"
                on:click={handleSubmit}>
            <h2>Create</h2>
        </button>
    </div>
</div>