<script>
        async function query(data) {
                const response = await fetch('https://api-inference.huggingface.co/models/stabilityai/stable-diffusion-2-1', {
                        headers: { Authorization: `Bearer ${import.meta.env.VITE_HUGGINGFACE_API_TOKEN}` },
                        method: 'POST',
                        body: JSON.stringify(data),
                });
                const blob = await response.blob();
                console.log(blob);
                return blob;
        }

        let input = 'Elephant';
        let output = [];

        const handleClick = () => {
                output = query({ inputs: input }).then((blob) => {
                        //turn base64 to image
                        console.log(blob);
                        return new Promise(function (resolve) {
                                let reader = new FileReader();
                                reader.readAsDataURL(blob);
                                reader.onload = () => resolve(reader.result);
                                reader.onerror = (error) => reject('Error: ', error);
                        });
                });
        };
</script>

<h1>Huggingface API Example Code</h1>
<p>
        This example uses the Stable Diffusion v2-1 Inference API. More about it here: <a href="https://huggingface.co/stabilityai/stable-diffusion-2-1">Stable Diffusion v2-1</a>
</p>
<div class="input">
        <p>
                Query: <input bind:value={input} />
        </p>
        <button on:click={handleClick}>Send API Request</button>
</div>
<p>
        ML created output:
        {#await output}
                <!-- output is pending -->
                API still loading.
        {:then output}
                <!-- output was fulfilled -->
                <img src={output} alt="img" />
                {input}
        {:catch error}
                <!-- output was rejected -->
                {'Something went wrong :('}
        {/await}
</p>

<style>
        .input {
                display: flex;
                align-items: center;
                gap: 12px;
        }

        button {
                width: fit-content;
                height: fit-content;
        }
</style>
