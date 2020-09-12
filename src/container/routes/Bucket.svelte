<script>

    import api from '../../api/service';
    import NavBar from '../../components/NavBar.svelte';

    export let name;
    let itens = {
        Contents: []
    }
    let error = false;
    let success = false;
    let message = ''
    let file;

    function carregarObjects(){
        error = false
        success = false
        api.get(`/s3/${name}/objects`).then((data) => {
            itens = data.data
        }).catch((err) => {
            message = 'Error listing objects'
            error = true
        })
    }

    function deleteObject(key){
        error = false
        success = false
        api.delete(`/s3/${name}/${key}`).then((data) => {
            message = 'Object Deleted'
            success = true
            carregarObjects()
        }).catch((err) => {
            message = 'Error deleting object'
            error = true;
        })
    }

    function handleFile(files){
        file = files[0]
    }

    function uploadFile(event){
        success = false
        error = false
        event.preventDefault()
        let formData = new FormData()
        formData.append('file', file)
        api.post(`/s3/${name}/upload`, formData, {
            headers: {
                'Content-Type': 'multipart/form-data'
            },
        }).then((data) => {
            message = 'Success Upload File'
            success = true
            carregarObjects()
        }).catch((err) => {
            message = 'Error Sending File'
            error = true
        })
    }

    function downloadFile(key){
        success = false
        error = false
        api.get(`/s3/${name}/${key}`).then((data) => {
            download(data, key)
        }).catch((err) => {
            console.log(err)
        })
    }

    function download(file, key) {
        const link = document.createElement('a')
        link.setAttribute('href', file.data.url)
        link.setAttribute('download', key)
        document.body.appendChild(link)
        link.click()
    }

    carregarObjects()
</script>
<style>
    .container-fluid{
        display: flex;
        margin-bottom: 10px;
    }
    h2 {
      margin-left: 10px;
    }
    .header{
      background-color: #ddd;
      padding-bottom: 10px;
      padding-top: 10px;
      align-items: center;
    }
    input{
        width: 70%;
    }
</style>
<main>
    <NavBar />
    <h2> List Objects</h2>
    <div class="container-fluid header">
        <div class="col-sm">
            Key
        </div>
        <div class="col-sm">
            Last Modified
        </div>
        <div class="col-sm">
            Size
        </div>
        <div class="col-sm">
            <form on:submit="{event => uploadFile(event)}">
                <input type="file" name="file" on:change="{event => handleFile(event.target.files)}" required/>
                <button type="submit"> upload </button>
            </form>
        </div>
        <div class="col-sm"></div>
    </div>
    {#if itens.Contents.length > 0}
        {#each itens.Contents as cont}
            <div class="container-fluid">
                <div class="col-sm">
                    {cont.Key}
                </div>
                <div class="col-sm">
                    {cont.LastModified}
                </div>
                <div class="col-sm">
                    {cont.Size}
                </div>
                <div class="col-sm">
                    <button on:click="{() => deleteObject(cont.Key)}"> Delete </button>
                </div>
                <div class="col-sm">
                    <button on:click="{() => downloadFile(cont.Key)}"> Download </button>
                </div>
            </div>
        {/each}
    {/if}
</main>