<template>
    <div id="app" class="container">
        <h1>HTTP com Axios</h1>
        <b-alert
            show
            dismissible
            :variant="mensagem.tipo"
            v-for="mensagem in mensagens"
            :key="mensagem.texto"
        >{{ mensagem.texto }}</b-alert>
        <b-card>
            <b-form-group label="Nome: ">
                <b-form-input
                    type="text"
                    size="lg"
                    v-model="usuario.nome"
                    palceholder="Informe o nome aqui"
                ></b-form-input>
            </b-form-group>
            <b-form-group label="Email: ">
                <b-form-input
                    type="email"
                    size="lg"
                    v-model="usuario.email"
                    palceholder="Informe o email aqui"
                ></b-form-input>
            </b-form-group>
            <hr />
            <b-button @click="salvar" size="lg" variant="primary">Salvar</b-button>
            <b-button class="ml-4" @click="obterUsuarios" size="lg" variant="success">Carregar</b-button>
        </b-card>
        <hr />
        <b-list-group>
            <b-list-group-item v-for="(usuario, id) in usuarios" :key="id">
                <strong>Nome: {{usuario.nome}}</strong>
                <br />
                <strong>Email: {{usuario.email}}</strong>
                <br />
                <strong>ID: {{id}}</strong>
                <br />
                <b-button variant="warning" @click="carregar(id)">Editar</b-button>
                <b-button class="ml-2" variant="danger" @click="deletar(id)">Delete</b-button>
            </b-list-group-item>
        </b-list-group>
    </div>
</template>

<script>
export default {
    /* 	created(){
		this.axios.post('usuarios.json', {
			nome: 'maria',
			email: 'maria@maria.com',
		}).then(res => console.log(res))
	} */
    data() {
        return {
            usuario: {
                nome: "",
                email: ""
            },
            usuarios: [],
            id: null,
            mensagens: []
        }
    },
    methods: {
        clear() {
            this.usuario.nome = ""
            this.usuario.email = ""
            this.id = null
            this.mensagens = []
        },
        salvar() {
            const metodo = this.id ? "patch" : "post"
            const finalURL = this.id ? `/${this.id}.json` : `.json`
            this.axios[metodo](`/usuarios${finalURL}`, this.usuario)

                .then(() => {
                    this.clear()

                    if (metodo === "patch") {
                        this.obterUsuarios()
                        this.mensagens.push({
                            texto: "Usuário salvo com sucesso!",
                            tipo: "success"
                        })
                    } else {
                        this.mensagens.push({
                            texto: "Usuário inserido com sucesso!",
                            tipo: "success"
                        })
                    }
                })
                .catch(err => {
                    this.mensagens.push({
                        texto: "Erro ao inserir! " + err,
                        tipo: "danger"
                    })
                })
        },
        obterUsuarios() {
            this.axios.get("usuarios.json").then(res => {
                this.usuarios = res.data
            })
        },
        carregar(id) {
            this.id = id
            this.usuario = { ...this.usuarios[id] }
        },
        deletar(id) {
            this.axios
                .delete(`/usuarios/${id}.json`)

                .then(() => {
                    this.clear()
                    this.mensagens.push({
                        texto: "Excluído com sucesso! ",
                        tipo: "success"
                    })
                    this.obterUsuarios()
                })

                .catch(err => {
                    this.mensagens.push({
                        texto: "Erro ao excluir! " + err,
                        tipo: "danger"
                    })
                })
        }
    }
}
</script>

<style>
#app {
    font-family: "Avenir", Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
    font-size: 1.5rem;
}

#app h1 {
    text-align: center;
    margin: 50px;
}
</style>
