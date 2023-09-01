<script lang="ts">
    import { onMount } from "svelte";
    // import { send } from 'vite';
    let texts: any;

    const discord_access_token = ""; // your discord access token
    const discord_channel = ""; // discord channel
    let message = "";

    onMount(() => {
        texts = document.querySelector(".texts");

        window.SpeechRecognition =
            window.SpeechRecognition || window.webkitSpeechRecognition;

        const recognition = new SpeechRecognition();
        recognition.interimResults = true;

        let p = document.createElement("p");

        recognition.addEventListener("result", (e) => {
            // texts.appendChild(p);
            const text = Array.from(e.results)
                .map((result) => result[0])
                .map((result) => result.transcript)
                .join("");
            console.log(text);
            p.innerText = text;
            p.appendChild(p);
            sendToDiscord(text, discord_access_token);

            if (e.results[0].isFinal) {
                p = document.createElement("p");
            }
        });

        recognition.addEventListener("end", () => {
            recognition.start();
        });

        recognition.start();
    });
    function send() {
        const p = document.createElement("p");
        p.innerText = message;
        texts.appendChild(p);

        sendToDiscord(message, discord_access_token);
    }

    function sendToDiscord(text: any, accessToken: any) {
        const url = `https://discord.com/api/v9/channels/${discord_channel}/messages`;
        //discord_channel id

        const payload = {
            content: text,
        };

        fetch(url, {
            method: "POST",
            headers: {
                "Content-Type": "application/json",
                Authorization: accessToken,
            },
            body: JSON.stringify(payload),
        })
            .then((response) => {
                if (response.ok) {
                    console.log("Message sent to Discord!");
                } else {
                    console.error(
                        "Failed to send message to Discord:",
                        response.status,
                        response.statusText
                    );
                }
            })
            .catch((error) => {
                console.error("Failed to send message to Discord:", error);
            });
    }
</script>

<section>
    <h1>Speech<br /> Recognition</h1>
    <p>Available In Chrome😎 Only</p>
    <div class="container">
        <input type="text" bind:value={message} />
        <button on:click={send}>Submit</button>

        <div class="texts" />
    </div>
</section>

<style>
    section {
        min-height: 100vh;
        width: 100%;
        display: flex;
        align-items: flex-start;
        background-color: rgb(37, 37, 37);
        flex-direction: column;
        padding: 50px 0;
    }
    section h1 {
        color: rgba(255, 255, 255, 0.322);
        text-align: center;
        width: 100%;
        font-size: 50px;
        margin-bottom: 10px;
    }
    section p {
        text-align: center;
        color: rgba(255, 255, 255, 0.322);
        width: 100%;
        margin-bottom: 40px;
    }
    .container {
        width: 90%;
        max-width: 500px;
        margin: 0 auto;
        justify-content: center;
    }
    :global(.texts p) {
        color: black;
        text-align: left;
        width: 100%;
        background-color: white;
        padding: 10px;
        border-radius: 8px;
        margin-bottom: 10px;
    }
    .texts p .replay {
        text-align: right;
    }
</style>
