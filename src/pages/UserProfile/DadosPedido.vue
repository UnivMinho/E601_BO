<template>
  <card class="card" :title="`Dados Pedido #${marcacoes.id}`">
    <div>
      <form @submit.prevent>
        <div class="row">
          <div class="col-md-5">
            <label>Atividade</label>
            <div class="dados">
              <p>{{ getNomeAtividade(marcacoes.idAtividade) }}</p>
            </div>
          </div>
          <div class="col-md-5">
            <label>Cliente</label>
            <div class="dados">
              <p>{{ getNomeCliente(marcacoes.id_cliente) }}</p>
            </div>
          </div>
          <div class="col-md-2">
            <label>Nº Pessoas</label>
            <div class="dados">
              <p>{{ marcacoes.numPessoas }}</p>
            </div>
          </div>
        </div>

        <div class="row">
          <div class="col-md-4">
            <label>Data</label>
            <div class="dados">
              <p>{{ marcacoes.dataReserva }}</p>
            </div>
          </div>
          <div class="col-md-4">
            <label>Hora Inicio</label>
            <div class="dados">
              <p>{{ marcacoes.horaInicio }}</p>
            </div>
          </div>
          <div class="col-md-4">
            <label>Hora Fim</label>
            <div class="dados">
              <p>{{ marcacoes.horaFim }}</p>
            </div>
          </div>
        </div>
        <h1 class="dados_cat">Dados do Responsável</h1>
        <div class="row">
          <div class="col-md-6">
            <label>Nome</label>
            <div class="dados">
              <p>{{ marcacoes.responsavel.nome }}</p>
            </div>
          </div>
          <div class="col-md-6">
            <label>Email</label>
            <div class="dados">
              <p>{{ marcacoes.responsavel.email }}</p>
            </div>
          </div>
        </div>

        <div class="row">
          <div class="col-md-6">
            <label>Telefone</label>
            <div class="dados">
              <p>{{ marcacoes.responsavel.telemovel }}</p>
            </div>
          </div>
          <div class="col-md-6">
            <label>Data de Nascimento</label>
            <div class="dados">
              <p>{{ marcacoes.responsavel.dataNascimento }}</p>
            </div>
          </div>
        </div>
        <h1 class="dados_cat">Dados para Faturação</h1>
        <div class="row">
          <div class="col-md-6">
            <label>Nome</label>
            <div class="dados">
              <p>{{ marcacoes.dadosFaturacao.nome }}</p>
            </div>
          </div>
          <div class="col-md-6">
            <label>Morada</label>
            <div class="dados">
              <p>{{ marcacoes.dadosFaturacao.morada }}</p>
            </div>
          </div>
        </div>

        <div class="row">
          <div class="col-md-6">
            <label>Nº Contribuinte</label>
            <div class="dados">
              <p>{{ marcacoes.dadosFaturacao.nif }}</p>
            </div>
          </div>
          <div class="col-md-6">
            <label>Código Postal</label>
            <div class="dados">
              <p>{{ marcacoes.dadosFaturacao.codigopostal }}</p>
            </div>
          </div>
        </div>
        <div class="row">
          <div class="col-md-12">
            <div class="form-group">
              <label>Atribua um funcionário responsável</label>
              <select class="custom-select" v-model="funcionarioSelecionado">
                <option disabled value="">Selecione uma opção</option>
                <option value="1">Funcionário 1</option>
                <option value="2">Funcionário 2</option>
                <option value="3">Funcionário 3</option>
              </select>
            </div>
          </div>
        </div>
        <div class="text-center">
          <div class="row">
            <div class="col-md-6">
              <p-button type="success" round @click.native.prevent="confirmarMarcacao">
                Confirmar Marcação
              </p-button>
            </div>
            <div class="col-md-6">
              <p-button type="danger" round @click.native.prevent="anularMarcacao">
                Anular Marcação
              </p-button>
            </div>
          </div>

        </div>
        <div class="clearfix"></div>
      </form>
    </div>
  </card>
</template>
<script>
import MarcacaoSucesso from "../Notifications/MarcacaoSucesso.vue";
import MarcacaoErro from "../Notifications/MarcacaoErro.vue";
export default {
  data() {
    const idMarcacao = this.$route.params.id;
    let marcacoesData = JSON.parse(localStorage.getItem("pedidos")) || {};
    return {
      marcacoes: marcacoesData.find(objeto => objeto.id == idMarcacao) || {},
      funcionarioSelecionado: null,
      type: ["", "info", "success", "warning", "danger"],
    };
  },
  methods: {
    confirmarMarcacao() {
      if (this.funcionarioSelecionado !== null) {
        const idMarcacao = this.$route.params.id;
        let marcacoesData = JSON.parse(localStorage.getItem("pedidos")) || [];
        let m = marcacoesData.find(objeto => objeto.id == idMarcacao) || {}
        marcacoesData.splice(m, 1)
        localStorage.setItem("pedidos", JSON.stringify(marcacoesData))
        m.funcResponsavel = this.funcionarioSelecionado
        m.estado = "Aceite"
        let reservasData = JSON.parse(localStorage.getItem("reservas")) || [];
        reservasData.push(m)
        localStorage.setItem("reservas", JSON.stringify(reservasData))
        this.$router.push('/pedidos');
        this.notifySucesso('top', 'center')
      } else {
        this.notifyErro('top', 'center')
      }

    },
    notifySucesso(verticalAlign, horizontalAlign) {
      this.$notify({
        component: MarcacaoSucesso,
        icon: "ti-check",
        horizontalAlign: horizontalAlign,
        verticalAlign: verticalAlign,
        type: "success",
      });
    },
    notifyErro(verticalAlign, horizontalAlign) {
      this.$notify({
        component: MarcacaoErro,
        icon: "ti-user",
        horizontalAlign: horizontalAlign,
        verticalAlign: verticalAlign,
        type: "warning",
      });
    },
    getNomeAtividade(id){
      let atividades = JSON.parse(localStorage.getItem('atividades'));
      let atividade = atividades.find(a => a.id === id);
      return atividade.nome;
    },
    getNomeCliente(id){
      let clientes = JSON.parse(localStorage.getItem('utilizadores'));
      let cliente = clientes.find(a => a.id === id);
      return cliente.nome;
    }
  },
};
</script>
<style>
.dados {
  background-color: #F4F3EF;
  box-shadow: 0 5px 5px rgba(0, 0, 0, 0.2);
  border-radius: 5px;
}

.dados p {
  padding: 10px 0px 10px 20px;
  font-size: 14px;
  font-weight: 400;
  color: #9A9A9A;
}

.dados_cat {
  font-size: 20px;
  color: #9A9A9A;
}
</style>
