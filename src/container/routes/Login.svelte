<script>
  import api from "../../api/service.js";
  import { navigate } from "svelte-routing";

  let email = "";
  let password = "";
  let error = false;
  let message = ''

  function login(event) {
    error = false;
    event.preventDefault();
    api
      .post("/auth", { email: email, password: password })
      .then((data) => {
        if (data.data.status === false || data.data.message === "Unauthorized"){
          error = true;
          message = data.data.message
        }
        else if (data.status === 200 
            && data.data.status === false 
            && data.data.message === "Password error") {
          error = true
          message = data.data.message
        }
        else {
          localStorage.setItem("token", data.data.token);
          navigate("/home", { data: data.data });
        }
      })
      .catch((err) => {
        error = true;
        console.log(err)
      });
  }

  function createAccount(){
      navigate('/create-account', { replace: true })
  }
</script>

<style>
  .container-fluid {
    display: flex;
    height: 100%;
    align-items: center;
    justify-content: center;
    background-color: #9466ff;
  }
  .login-content {
    width: 50%;
    margin: 10px;
    padding: 15px;
    background-color: white;
    border-radius: 5px;
  }
  .btn {
    width: 100%;
  }
  .alert {
    margin: 0;
  }
  .divider{
      height: 10px;
  }
</style>

{#if error}
  <div class="alert alert-warning alert-dismissible fade show" role="alert">
    <strong>Error!</strong> {message} <button
      type="button"
      class="close"
      data-dismiss="alert"
      aria-label="Close">
      <span aria-hidden="true">&times;</span>
    </button>
  </div>
{/if}
<div class="container-fluid">
  <div class="login-content">
    <form on:submit={(event) => login(event)}>
      <div class="form-group">
        <label for="exampleInputEmail1">Email address</label>
        <input
          type="email"
          class="form-control"
          id="exampleInputEmail1"
          aria-describedby="emailHelp"
          on:change={(event) => (email = event.target.value)} required/>
        <small id="emailHelp" class="form-text text-muted">We'll never share
          your email with anyone else.</small>
      </div>
      <div class="form-group">
        <label for="exampleInputPassword1">Password</label>
        <input
          type="password"
          class="form-control"
          id="exampleInputPassword1"
          on:change={(event) => (password = event.target.value)} required/>
      </div>
      <button type="submit" class="btn btn-primary">Login</button>
    </form>
    <div class="divider" />
    <button type="button" on:click="{() => createAccount()}" class="btn btn-info">Create Account</button>
  </div>
</div>
