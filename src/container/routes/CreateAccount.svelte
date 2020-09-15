<script>
import { navigate } from 'svelte-routing';

  import api from '../../api/service';

  let email = "";
  let password = "";
	let name = "";
	let message = "";

	let error = false;

  const token = `Bearer ${localStorage.getItem("token")}`;
	
	function createUser(event){
		error = false
		event.preventDefault()
		const body = {
			name: name,
			email: email,
			password: password,
		}
		api.post('/user', body, {headers: {Authorization: token}}).then((data) => {
			if(typeof data.data.keyValue !== undefined || data.data.keyValue !== null ){
				message = `${data.data.keyValue.email} email already exists`
				error = true
			}
			else{
				navigate('/', {replace: true, state: true})
			}
		}).catch((err) => {
			console.log(err)
			message = 'Create Acccount error'
			error = true
		})
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
  .btn{
      width: 100%;
  }
	.alert{
		margin: 0;
	}
</style>
{#if error}
	<div class="alert alert-warning alert-dismissible fade show" role="alert">
		<strong>Error</strong> {message}
		<button type="button" class="close" data-dismiss="alert" aria-label="Close">
			<span aria-hidden="true">&times;</span>
		</button>
	</div>
{/if}
<div class="container-fluid">
  <div class="login-content">
    <form on:submit="{event => createUser(event)}">
      <div class="form-group">
        <label for="exampleInputEmail1">Email address</label>
        <input
          type="email"
          class="form-control"
          id="exampleInputEmail1"
          aria-describedby="emailHelp"
          on:change={(event) => (email = event.target.value)}
          required />
        <small id="emailHelp" class="form-text text-muted">We'll never share
          your email with anyone else.</small>
      </div>
      <div class="form-group">
        <label for="exampleInputName1">Name</label>
        <input
          type="text"
          class="form-control"
          id="exampleInputName1"
          aria-describedby="emailHelp"
          on:change={(event) => (name = event.target.value)}
          required />
        <small id="emailHelp" class="form-text text-muted">We'll never share
          your email with anyone else.</small>
      </div>
      <div class="form-group">
        <label for="exampleInputPassword1">Password</label>
        <input
          type="password"
          class="form-control"
          id="exampleInputPassword1"
          on:change={(event) => (password = event.target.value)}
          required />
      </div>
      <button type="submit" class="btn btn-primary">Create</button>
    </form>
  </div>
</div>
