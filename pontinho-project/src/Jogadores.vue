<template>
  <div class="container col-12">
    <div class="d-flex justify-content-center align-items-center gap-3 w-100">
      <button
        type="button"
        class="btn btn-custom flex-fill col-4"
        @click="somar()"
      >
      <i class="bi bi-plus-circle me-2"></i>SOMAR
      </button>
      <button
        type="button"
        class="btn btn-custom flex-fill col-4"
        @click="novaRodada()"
      >
        <i class="bi bi-arrow-repeat me-2"></i>NOVA RODADA
      </button>
      <button
        type="button"
        class="btn btn-custom flex-fill col-4"
        @click="novoJogo()"
      >
        <i class="bi bi-arrow-clockwise me-2"></i>NOVO JOGO
      </button>
      <button
        type="button"
        class="btn btn-custom flex-fill col-4"
        @click="novoJogador()"
      >
        <i class="bi bi-person-plus me-2"></i>NOVO JOGADOR
      </button>
    </div>
  </div>

  <div class="container py-4">
    <!-- Cabeçalho -->
    <div class="row mb-3">
      <div class="col-4 fw-bold h4" style="color:white">Jogadores</div>
      <div class="col-2 fw-bold h4" style="color:white">Pontos</div>
      <div class="col-2 fw-bold h4" style="color:white">Total</div>
      <div class="col-2 fw-bold h4" style="color:white">Escape</div>
    </div>

    <!-- Lista de jogadores -->
    <div
      v-for="(jogador, index) in jogadores"
      :key="index"
      class="row align-items-center mb-2"
    >
      <div class="col-4">
        <input
          type="text"
          class="form-control input-custom"
          v-model="jogador.nome"
          @input="jogador.nome = jogador.nome.toUpperCase()"
        />
      </div>
      <div class="col-2">
        <input
          type="number"
          class="form-control input-custom text-center"
          v-model="jogador.pontos"
        />
      </div>
      <div class="col-2">
        <input
          disabled
          type="text"
          class="form-control input-custom text-center"
          v-model="jogador.total"
        />
      </div>
      <div class="col-2">
        <input
          disabled
          type="text"
          class="form-control input-custom-escape text-center"
          v-model="jogador.escape"
        />
      </div>
      <div v-if="jogador.eliminado" class="col-2 d-flex gap-2">
        <button
          type="button"
          class="btn btn-success btn-jogador-eliminado"
          @click="continuarJogador(jogador)"
        >
          CONTINUAR
        </button>
        <button
          type="button"
          class="btn btn-danger btn-jogador-eliminado"
          @click="removerJogador(jogador)"
        >
          SAIR
        </button>
      </div>
      <div v-if="jogador.quantidadeEntrada > 0 && !jogador.eliminado" class="col-2 d-flex gap-2">
        <h5 style="color: white;">Entrada(s): {{ jogador.quantidadeEntrada }}</h5>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Jogadores",
  data() {
    return {
      jogadores: this.inicializarJogadores(),
    };
  },
  methods: {
    inicializarJogadores() {
      return [
        { nome: "", pontos: "", total: 0, escape: 99, eliminado: false, quantidadeEntrada: 0 },
        { nome: "", pontos: "", total: 0, escape: 99, eliminado: false, quantidadeEntrada: 0 },
        { nome: "", pontos: "", total: 0, escape: 99, eliminado: false, quantidadeEntrada: 0 },
        { nome: "", pontos: "", total: 0, escape: 99, eliminado: false, quantidadeEntrada: 0 },
        { nome: "", pontos: "", total: 0, escape: 99, eliminado: false, quantidadeEntrada: 0 },
        { nome: "", pontos: "", total: 0, escape: 99, eliminado: false, quantidadeEntrada: 0 },
      ];
    },

    somar() {
      const nenhumJogadorPreenchido = this.jogadores.every(
        (jogador) => jogador.nome.trim() === ""
      );

      if (nenhumJogadorPreenchido) {
        this.$swal({
          title: "Alerta!",
          text: "Preencha o nome dos jogadores!",
          icon: "warning",
          timer: 3000,
          showConfirmButton: false,
        });

        return;
      }

      const existeJogadorEliminado = this.jogadores.some(
        (jogador) => jogador.eliminado == true
      );

      if (existeJogadorEliminado) {
        this.$swal({
          title: "Alerta!",
          text: "Há jogadores eliminados, resolva isso antes de fazer a soma.",
          icon: "warning",
          timer: 3000,
          showConfirmButton: false,
        });

        return;
      }

      this.jogadores = this.jogadores.filter(
        (jogador) => jogador.nome.trim() !== "" && jogador.eliminado == false
      );

      this.jogadores.forEach((jogador) => {
        if (!jogador.pontos) jogador.pontos = 0;

        jogador.total =
          (parseInt(jogador.pontos) || 0) + parseInt(jogador.total);

        if (parseInt(jogador.total) >= 100) {
          jogador.nome += " - ELIMINADO";
          jogador.total = "FORA";
          jogador.escape = "FORA";
          jogador.eliminado = true;
        } else {
          jogador.escape = 99 - jogador.total;
        }

        jogador.pontos = "";
      });
    },

    novaRodada() {
      this.$swal({
        title: "Atenção!",
        text: "Deseja iniciar uma nova rodada com os mesmos jogadores?",
        icon: "warning",
        showConfirmButton: true,
        showCancelButton: true,
        confirmButtonText: "Sim",
        cancelButtonText: "Não"
      }).then((result) => {
        if (result.isConfirmed) {
          this.jogadores.forEach((jogador) => { 
            jogador.nome = jogador.nome.replace(" - ELIMINADO", "");
            jogador.pontos = "";
            jogador.total = 0;
            jogador.escape = 99;
            jogador.eliminado = false;
            jogador.quantidadeEntrada = 0;
          });
        }
      });
    },

    novoJogo() {
      this.$swal({
        title: "Atenção!",
        text: "Deseja iniciar um novo jogo?",
        icon: "warning",
        showConfirmButton: true,
        showCancelButton: true,
        confirmButtonText: "Sim",
        cancelButtonText: "Não"
      }).then((result) => {
        if (result.isConfirmed) {
          this.jogadores = this.inicializarJogadores();
        }
      });
    },

    novoJogador() {
      const jogadorComMaiorPontuacao = this.jogadores.filter(x => x.eliminado !== true).reduce(
        (max, jogador) => (jogador.total > max.total ? jogador : max),
        { total: 0 }
      );

      this.jogadores.push({
        nome: "",
        pontos: "",
        total: jogadorComMaiorPontuacao.total ?? 0,
        escape: jogadorComMaiorPontuacao.escape ?? 99,
        eliminado: false,
        quantidadeEntrada: 0
      });
    },

    continuarJogador(jogador) {
      const jogadorComMaiorPontuacao = this.jogadores.filter(x => x.eliminado !== true).reduce(
        (max, jogador) => (jogador.total > max.total ? jogador : max),
        { total: 0 }
      );

      jogador.nome = jogador.nome.replace(" - ELIMINADO", "");
      jogador.eliminado = false;
      jogador.quantidadeEntrada += 1;
      jogador.pontos = "";
      jogador.total = jogadorComMaiorPontuacao.total ?? 0;
      jogador.escape = jogadorComMaiorPontuacao.escape ?? 99;
    },

    removerJogador(jogador) {
      this.jogadores = this.jogadores.filter((j) => j !== jogador);
    },

    handleBeforeUnload(event) {
      event.preventDefault();
      event.returnValue = '';
    },
  },

  mounted() {
    window.addEventListener('beforeunload', this.handleBeforeUnload);
  },

  beforeDestroy() {
    window.removeEventListener('beforeunload', this.handleBeforeUnload);
  },
};
</script>

<style scoped>
.btn-custom {
  border-radius: 25px;
  font-size: 1.6rem;
  margin-top: 20px;
  margin-bottom: 20px;
  padding: 20px 0;
  color: #eafaf1;
  background: linear-gradient(90deg, #28a74600 0%, #33ca65 100%);
  border: 1px solid #145214;
  box-shadow: 0 4px 24px rgba(0, 0, 0, 0.25), 0 1px 8px rgba(0, 0, 0, 0.5);
  transition: transform 0.15s, box-shadow 0.15s, background 0.3s;
  font-weight: 600;
  letter-spacing: 1px;
  height: 100px;
}

.btn-custom:hover,
.btn-custom:focus {
  background: linear-gradient(90deg, #145214 0%, #43ea7a 100%);
  color: #fff;
  box-shadow: 0 8px 32px rgba(40, 167, 69, 0.35), 0 2px 16px rgba(0,0,0,0.7);
  outline: none;
}

.btn-custom:active {
  transform: translateY(2px) scale(0.98);
  background: linear-gradient(90deg, #145214 0%, #28a745 100%);
  box-shadow: 0 4px 16px rgba(40, 167, 69, 0.25), 0 1px 8px rgba(0,0,0,0.5);
}

.btn-jogador-eliminado {
  border-radius: 10px;
  font-size: 1.2rem;
  padding: 10px 15px;
}

.input-custom {
  font-size: 1.8rem;
  font-weight: bold;
  padding: 12px 20px;
  border-radius: 15px;
  box-shadow: 0 2px 8px rgba(40, 167, 69, 0.1);
  transition: border-color 0.2s, box-shadow 0.2s;
}

.input-custom:focus {
  border-color: #28a745; /* verde Bootstrap */
  box-shadow: 0 4px 16px rgba(40, 167, 69, 0.18); /* verde mais forte */
  outline: none;
}

.input-custom-escape {
  font-size: 1.8rem;
  font-weight: bold;
  padding: 12px 20px;
  border-radius: 15px;
  box-shadow: 0 2px 8px rgba(40, 167, 69, 0.1);
  transition: border-color 0.2s, box-shadow 0.2s;
  background: rgba(0, 0, 0, 0.3); /* fundo opaco/transparente */
  color: #fff; /* texto branco */
}
</style>
