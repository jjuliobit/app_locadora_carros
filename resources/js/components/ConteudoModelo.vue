<template>
    <!-- Componente de Card personalizado -->
    <div>
        <table class="table">
            <thead>
                <tr>
                    <th class="col-7" v-for="t, key in titulos" :key="key">{{ t.titulo }}</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="obj, chave in dadosFiltrados" :key="chave">
                    <td v-for="valor, chaveValor in obj" :key="chaveValor">
                        <span v-if="titulos[chaveValor].tipo == 'texto'"> {{ valor }}</span>
                        <span v-if="titulos[chaveValor].tipo == 'imagem'">
                            <img :src="'storage/' + valor" height="50" width="50">
                        </span>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>



<script>
export default {
    data() {
        return {
            imagemCard: '',
        }
    },
    props: ["dados", "titulos"],
    methods: {
        exibirImagem(valor, chaveValor) {
            if (this.titulos[chaveValor].tipo === 'imagem') {
                this.imagemCard = 'storage/' + valor;
            }
        }
    },
    computed: {
        dadosFiltrados() {
            let campos = Object.keys(this.titulos);
            let dadosFiltrados = [];

            this.dados.map((item, chave) => {
                let itemFiltrado = {};
                campos.forEach((campo) => {
                    itemFiltrado[campo] = item[campo]; //utilizar a sintaxe de array para atribuir valores a objetos
                });
                dadosFiltrados.push(itemFiltrado);
            });

            return dadosFiltrados; //retorne um array de objetos
        },
    },
};
</script>
