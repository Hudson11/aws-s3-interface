<script>
    import { navigate } from 'svelte-routing'
    function logout(){
        localStorage.removeItem('token')
        navigate('/', {logout: true})
    }
</script>

<div class="container-fluid">
    <p> BEM VINDO </p>
    <button on:click="{() => logout()}"> Sair </button>
</div>

<style>
    .container-fluid{
        height: 80px;
        background-color: #9466ff;
        display: flex;
        align-items: center;
        padding-left: 10px;
        padding-right: 10px;
        justify-content: space-between;
    }
    p{
        font-size: 16px;
        font-weight: bold;
        color: white;
        margin: 0;
    }
    button{
        color: white;
        border: none;
        background-color: transparent;
        cursor: pointer;
    }
</style>