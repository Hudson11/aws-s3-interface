<script>
  import { each } from "svelte/internal";
  import NavBar from "../../components/NavBar.svelte";
  import api from "../../api/service";
  import { Link } from "svelte-routing";

  let bucketData = { Buckets: [] };
  let error = false;
  let bucketName = "";
  let message = "";
  let file;

  const token = `Bearer ${localStorage.getItem("token")}`;
  // chamada get ao recurso /s3 => listar buckets existentes
  function getBuckets() {
    api
      .get("/s3", { headers: { Authorization: token } })
      .then((data) => {
        bucketData = data.data;
        error = false;
      })
      .catch((err) => {
        error = true;
        message = "Error getting list of Buckets";
      });
  }

  function deleteBucket({ name }) {
    api
      .delete(`/s3/${name}`, { headers: { Authorization: token } })
      .then((data) => {
        getBuckets();
        error = false;
      })
      .catch((err) => {
        error = true;
        message = "Error delete Bucket";
      });
  }

  function createBucket(event, { name }) {
    event.preventDefault();
    api
      .post("/s3", { name: name }, { headers: { Authorization: token } })
      .then((data) => {
        getBuckets();
        error = false;
      })
      .catch((err) => {
        console.log(err);
        error = true;
        message = "Error create Bucket";
      });
  }

  function bucketNameChange(event) {
    bucketName = event.target.value;
  }

  function handleFile(event) {
    file = event.target.value;
  }

  function submitFile() {}

  getBuckets();
</script>

<style>
  h2 {
    margin-left: 10px;
  }
  .remove-btn {
    border: none;
    border-radius: 5px;
    padding: 10px;
    background-color: white;
    color: #f44336;
    font-size: 12px;
    cursor: pointer;
    margin: 0;
  }
  .remove-btn:hover {
    box-shadow: #f44336 2px 2px 5px;
    background-color: #f44336;
    color: white;
    transition: 0.2s;
  }
  .new-btn {
    padding: 10px;
    height: 45px;
    border: none;
    border-radius: 5px;
    background-color: #43a047;
    font-size: 12px;
    color: white;
    cursor: pointer;
    margin: 0;
  }
  form {
    display: flex;
    flex-direction: row;
    align-items: center;
  }
  input {
    margin: 10px;
  }
  .container-fluid {
    display: flex;
    flex-direction: row;
    margin-bottom: 10px;
  }
  .header {
    background-color: #ddd;
    padding-bottom: 10px;
    padding-top: 10px;
  }
</style>

<main>
  <NavBar />
  {#if error}
    <div class="alert alert-danger alert-dismissible fade show" role="alert">
      <strong>Message</strong>
      {message}
      <button
        type="button"
        class="close"
        data-dismiss="alert"
        aria-label="Close">
        <span aria-hidden="true">&times;</span>
      </button>
    </div>
  {/if}
  <h2>Create New Bucket</h2>
  <form on:submit={(event) => createBucket(event, { name: bucketName })}>
    <div class="div col">
      <input
        class="form-control"
        id="bucket"
        required
        on:change={(event) => bucketNameChange(event)} />
    </div>
    <div class="div col">
      <button type="submit" class="new-btn">New Bucket</button>
    </div>
  </form>
  <h2>Buckets</h2>
  <div class="container-fluid header">
    <div class="col-sm">Name</div>
    <div class="col-sm">Creation Date</div>
    <div class="col-sm" />
    <div class="col-sm" />
  </div>
  {#if bucketData.Buckets.length !== 0}
    {#each bucketData.Buckets as buck}
      <div class="container-fluid">
        <div class="col-sm">{buck.Name}</div>
        <div class="col-sm">{buck.CreationDate}</div>
        <div class="col-sm">
          <button
            class="remove-btn"
            on:click={() => deleteBucket({ name: buck.Name })}>
            Remove
          </button>
        </div>
        <div class="col-sm">
          <Link to={`bucket/${buck.Name}`}>
            <button class="new-btn"> View Bucket </button>
          </Link>
        </div>
      </div>
    {/each}
  {/if}
</main>
