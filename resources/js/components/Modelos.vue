<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">


                <!-- início do card de busca -->
                <card-component titulo="Busca de modelos">
                    <template v-slot:conteudo>
                        <div class="form-row">
                            <div class="col mb-3">
                                <input-container-component titulo="ID" id="inputId" id-help="idHelp"
                                    texto-ajuda="Opcional. Informe o ID da modelo">
                                    <input type="text" class="form-control" id="inputId" aria-describedby="idHelp"
                                        placeholder="ID" v-model="busca.id">
                                </input-container-component>
                            </div>
                            <div class="col mb-3">
                                <input-container-component titulo="Marca do id" id="inputNome" id-help="nomeHelp"
                                    texto-ajuda="Opcional. Informe o marca do ID">
                                    <input type="text" class="form-control" id="inputNome" aria-describedby="nomeHelp"
                                        placeholder="Marca do ID" v-model="busca.marca_id">
                                </input-container-component>
                            </div>
                            <div class="col mb-3">
                                <input-container-component titulo="Nome da modelo" id="inputNome" id-help="nomeHelp"
                                    texto-ajuda="Opcional. Informe o nome da modelo">
                                    <input type="text" class="form-control" id="inputNome" aria-describedby="nomeHelp"
                                        placeholder="Nome da modelo" v-model="busca.nome">
                                </input-container-component>
                            </div>
                            <div class="col mb-3">
                                <input-container-component titulo="Numero portas" id="inputNome" id-help="nomeHelp"
                                    texto-ajuda="Opcional. Informe o quantidade da porta">
                                    <input type="text" class="form-control" id="inputNome" aria-describedby="nomeHelp"
                                        placeholder="Numeros da portas" v-model="busca.numero_portas">
                                </input-container-component>
                            </div>
                        </div>
                    </template>

                    <template v-slot:rodape>
                        <button type="submit" class="btn btn-primary btn-sm float-right"
                            @click="pesquisar()">Pesquisar</button>
                    </template>
                </card-component>
                <!-- fim do card de busca -->


                <!-- início do card de listagem de modelos -->
                <card-component titulo="Relação de modelos">
                    <template v-slot:conteudo>
                        <table-component :dados="modelos.data"
                            :visualizar="{ visivel: true, dataToggle: 'modal', dataTarget: '#modalModeloVisualizar' }"
                            :atualizar="{ visivel: true, dataToggle: 'modal', dataTarget: '#modalModeloAtualizar' }"
                            :remover="{ visivel: true, dataToggle: 'modal', dataTarget: '#modalModeloRemover' }" :titulos="{
                                id: { titulo: 'ID', tipo: 'texto' },
                                marca_id: { titulo: 'Marca do ID', tipo: 'texto' },
                                nome: { titulo: 'Nome do modelo', tipo: 'texto' },
                                imagem: { titulo: 'Imagem', tipo: 'imagem' },
                                numero_portas: { titulo: 'Numeros Portas', tipo: 'texto' },
                                lugares: { titulo: 'Lugares', tipo: 'texto' },
                                air_bag: { titulo: 'Air Bag', tipo: 'texto' },
                                abs: { titulo: 'ABS', tipo: 'texto' },
                                created_at: { titulo: 'Criação de data', tipo: 'data' },
                            }">
                        </table-component>
                    </template>

                    <template v-slot:rodape>
                        <div class="row">
                            <div class="col-10">
                                <paginate-component>
                                    <li v-for="l, key in modelos.links" :key="key"
                                        :class="l.active ? 'page-item active' : 'page-item'" @click="paginacao(l)">
                                        <a class="page-link" v-html="l.label"></a>
                                    </li>
                                </paginate-component>
                            </div>

                            <div class="col">
                                <button type="button" class="btn btn-primary btn-sm float-right" data-toggle="modal"
                                    data-target="#modalModelo">Adicionar</button>
                            </div>
                        </div>
                    </template>
                </card-component>
                <!-- fim do card de listagem de modelos -->
            </div>
        </div>


        <!-- início do modal de inclusão de modelo -->
        <modal-component id="modalModelo" titulo="Adicionar modelo">

            <template v-slot:alertas>
                <alert-component tipo="success" :detalhes="transacaoDetalhes" titulo="Cadastro realizado com sucesso"
                    v-if="transacaoStatus == 'adicionado'"></alert-component>
                <alert-component tipo="danger" :detalhes="transacaoDetalhes" titulo="Erro ao tentar cadastrar o modelo"
                    v-if="transacaoStatus == 'erro'"></alert-component>
            </template>

            <template v-slot:conteudo>
                <div class="form-group">
                    <input-container-component titulo="ID da marca" id="novoMarcaIDModelo " id-help="novoMarcaIDModeloHelp"
                        texto-ajuda="Informe ID da marca">
                        <input type="text" class="form-control" id="novoMarcaIDModelo"
                            aria-describedby="novoMarcaIDModeloHelp" placeholder="ID da marca" v-model="marcaIDModelo">
                    </input-container-component>
                    {{ marcaIDModelo }}
                </div>

                <div class="form-group">
                    <input-container-component titulo="Nome do modelo" id="novoNomeModelo" id-help="novoNomeModeloHelp"
                        texto-ajuda="Informe o nome do modelo">
                        <input type="text" class="form-control" id="novoNomeModelo" aria-describedby="novoNomeModeloHelp"
                            placeholder="Nome do modelo" v-model="nomeModelo">
                    </input-container-component>
                    {{ nomeModelo }}
                </div>

                <div class="form-group">
                    <input-container-component titulo="Imagem" id="novoImagemModelo" id-help="novoImagemModeloHelp"
                        texto-ajuda="Selecione uma imagem no formato PNG">
                        <input type="file" class="form-control-file" id="novoImagemModelo"
                            aria-describedby="novoImagemModeloHelp" placeholder="Selecione uma imagem"
                            @change="carregarImagem($event)">
                    </input-container-component>
                    <img :src="arquivoImagem">
                </div>

                <div class="form-group">
                    <input-container-component titulo="Numeros Portas" id="novoNumerosPortas"
                        id-help="novoNumerosPortasHelp" texto-ajuda="Informe  os numeros portas">
                        <input type="text" class="form-control" id="novoNumerosPortas"
                            aria-describedby="novoNumerosPortasHelp" placeholder="Numero portas" v-model="numerosPortas">
                    </input-container-component>
                    {{ numerosPortas }}

                    <div class="form-group">
                        <input-container-component titulo="Lugares" id="novoLugares" id-help="novoLugaresHelp"
                            texto-ajuda="Informe o lugar">
                            <input type="text" class="form-control" id="novoLugaresHelp" aria-describedby="novoLugaresHelp"
                                placeholder="Lugares" v-model="lugaresModelos">
                        </input-container-component>
                        {{ lugaresModelos }}
                    </div>
                    <div class="form-group">
                        <input-container-component titulo="Air Bag" id="novoAirBag" id-help="novoAirBagHelp"
                            texto-ajuda="Informe o AIR BAG">
                            <input type="text" class="form-control" id="novoAirBag" aria-describedby="novoAirBagHelp"
                                placeholder="AIR BAG" v-model="airBagModelos">
                        </input-container-component>
                        {{ airBagModelos }}
                    </div>
                    <div class="form-group">
                        <input-container-component titulo="Abs" id="novoAbs" id-help="novoAbsHelp"
                            texto-ajuda="Informe o Abs">
                            <input type="text" class="form-control" id="novoAbs" aria-describedby="novoAbsHelp"
                                placeholder="Abs" v-model="absModelos">
                        </input-container-component>
                        {{ absModelos }}
                    </div>
                </div>
            </template>

            <template v-slot:rodape>
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Fechar</button>
                <button type="button" class="btn btn-primary" @click="salvar()">Salvar</button>
            </template>
        </modal-component>
        <!-- fim do modal de inclusão de modelo -->

        <!-- início do modal de visualização de modelo -->
        <modal-component id="modalModeloVisualizar" titulo="Visualizar modelo">
            <template v-slot:alertas></template>
            <template v-slot:conteudo>
                <input-container-component titulo="ID">
                    <input type="text" class="form-control" :value="$store.state.item.id" disabled>
                </input-container-component>

                <input-container-component titulo="ID da marca">
                    <input type="text" class="form-control" :value="$store.state.item.marca_id" disabled>
                </input-container-component>

                <input-container-component titulo="Nome da modelo">
                    <input type="text" class="form-control" :value="$store.state.item.nome" disabled>
                </input-container-component>

                <input-container-component titulo="Imagem">
                    <img :src="'storage/' + $store.state.item.imagem" v-if="$store.state.item.imagem">
                </input-container-component>

                <input-container-component titulo="Lugares">
                    <input type="text" class="form-control" :value="$store.state.item.lugares" disabled>
                </input-container-component>
                <input-container-component titulo="Air Bags">
                    <input type="text" class="form-control" :value="$store.state.item.air_bag" disabled>
                </input-container-component>
                <input-container-component titulo="ABS">
                    <input type="text" class="form-control" :value="$store.state.item.abs" disabled>
                </input-container-component>
                <input-container-component titulo="Data de criação">
                    <input type="text" class="form-control" :value="$store.state.item.created_at" disabled>
                </input-container-component>
            </template>
            <template v-slot:rodape>
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Fechar</button>
            </template>
        </modal-component>
        <!-- fim do modal de visualização de modelo -->


        <!-- início do modal de remoção de modelo -->
        <modal-component id="modalModeloRemover" titulo="Remover modelo">
            <template v-slot:alertas>
                <alert-component tipo="success" titulo="Transação realizada com sucesso" :detalhes="$store.state.transacao"
                    v-if="$store.state.transacao.status == 'sucesso'"></alert-component>
                <alert-component tipo="danger" titulo="Erro na transação" :detalhes="$store.state.transacao"
                    v-if="$store.state.transacao.status == 'erro'"></alert-component>
            </template>
            <template v-slot:conteudo v-if="$store.state.transacao.status != 'sucesso'">
                <input-container-component titulo="ID">
                    <input type="text" class="form-control" :value="$store.state.item.id" disabled>
                </input-container-component>

                <input-container-component titulo="Nome da modelo">
                    <input type="text" class="form-control" :value="$store.state.item.nome" disabled>
                </input-container-component>
            </template>
            <template v-slot:rodape>
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Fechar</button>
                <button type="button" class="btn btn-danger" @click="remover()"
                    v-if="$store.state.transacao.status != 'sucesso'">Remover</button>
            </template>
        </modal-component>
        <!-- fim do modal de remoção de modelo -->

        <!-- início do modal de atualização de modelo -->
        <modal-component id="modalModeloAtualizar" titulo="Atualizar modelo">

            <template v-slot:alertas>
                <alert-component tipo="success" titulo="Transação realizada com sucesso" :detalhes="$store.state.transacao"
                    v-if="$store.state.transacao.status == 'sucesso'"></alert-component>
                <alert-component tipo="danger" titulo="Erro na transação" :detalhes="$store.state.transacao"
                    v-if="$store.state.transacao.status == 'erro'"></alert-component>
            </template>

            <template v-slot:conteudo>
                <div class="form-group">
                    <input-container-component titulo="Nome da modelo" id="atualizarNome" id-help="atualizarNomeHelp" 
                        texto-ajuda="Informe o nome da modelo">
                        <input type="text" class="form-control" id="atualizarNome" aria-describedby="atualizarNomeHelp"
                            placeholder="Nome da modelo" v-model="$store.state.item.nome">
                    </input-container-component>
                </div>

                <div class="form-group">
                    <input-container-component titulo="Marca do ID" id="atualizarMarcaID" id-help="atualizarMarcaIDHelp"
                        texto-ajuda="Informe a marca do ID">
                        <input type="text" class="form-control" id="atualizarMarcaID" aria-describedby="atualizarMarcaIDHelp" 
                            placeholder="Marca do ID" v-model="$store.state.item.marca_id">
                    </input-container-component>
                </div>
                <div class="form-group">
                    <input-container-component titulo="Numero portas" id="atualizarNumeroPortas"
                        id-help="atualizarNumeroPortasHelp" texto-ajuda="Informe o numero portas">
                        <input type="text" class="form-control" id="atualizarNumeroPortas"
                            aria-describedby="atualizarNumeroPortasHelp" placeholder="Numero portas"
                            v-model="$store.state.item.numero_portas">
                    </input-container-component>
                </div>

                <div class="form-group">
                    <input-container-component titulo="Lugares" id="atualizarLugares" id-help="atualizarLugaresHelp"
                        texto-ajuda="Informe as lugares">
                        <input type="text" class="form-control" id="atualizarLugares"
                            aria-describedby="atualizarLugaresHelp" placeholder="Lugares"
                            v-model="$store.state.item.lugares">
                    </input-container-component>
                </div>
                <div class="form-group">
                    <input-container-component titulo="Air Bag" id="atualizarAirBag" id-help="atualizarAirBagHelp"
                        texto-ajuda="Informe as Air Bag">
                        <input type="text" class="form-control" id="atualizarAirBag"
                            aria-describedby="atualizarAirBagHelp" placeholder="Air Bag"
                            v-model="$store.state.item.air_bag">
                    </input-container-component>
                </div>
                <div class="form-group">
                    <input-container-component titulo="Abs" id="atualizarAbs" id-help="atualizarAbsHelp"
                        texto-ajuda="Informe Abs">
                        <input type="text" class="form-control" id="atualizarAbs"
                            aria-describedby="atualizarAbsHelp" placeholder="Abs"
                            v-model="$store.state.item.abs">
                    </input-container-component>
                </div>

                <div class="form-group">
                    <input-container-component titulo="Imagem" id="atualizarImagem" id-help="atualizarImagemHelp"
                        texto-ajuda="Selecione uma imagem no formato PNG">
                        <input type="file" class="form-control-file" id="atualizarImagem"
                            aria-describedby="atualizarImagemHelp" placeholder="Selecione uma imagem"
                            @change="carregarImagem($event)">    
                    </input-container-component>
                    <img :src="arquivoImagem">
                </div>
            </template>

            <template v-slot:rodape>
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Fechar</button>
                <button type="button" class="btn btn-primary" @click="atualizar()">Atualizar</button>
            </template>
        </modal-component>
        <!-- fim do modal de inclusão de modelo -->
    </div>
</template>

<script>
import Paginate from './Paginate.vue'
export default {
    data() {
        return {
            urlBase: 'http://localhost:8000/api/v1/modelo',
            urlPaginacao: '',
            urlFiltro: '',
            marcaIDModelo: '',
            nomeModelo: '',
            arquivoImagem: [],
            imgSave: [],
            numerosPortas: '',
            lugaresModelos: '',
            airBagModelos: '',
            absModelos: '',
            transacaoStatus: '',
            transacaoDetalhes: {},
            modelos: { data: [] },
            busca: { id: '', nome: '' }
        }
    },
    methods: {
        atualizar() {
            let url = this.urlBase + '/' + this.$store.state.item.id
            
            let formData = new FormData();
            formData.append('_method', 'patch')
            formData.append('marca_id', this.$store.state.item.marca_id);
            formData.append('nome', this.$store.state.item.nome)
            formData.append('numero_portas', this.$store.state.item.numero_portas);
            formData.append('lugares', this.$store.state.item.lugares);
            formData.append('air_bag', this.$store.state.item.air_bag);
            formData.append('abs', this.$store.state.item.abs);

            if (this.imgSave[0]) {
                formData.append('imagem', this.imgSave[0])
            }



            let config = {
                headers: {
                    'Content-Type': 'multipart/form-data',
                }
            }

            axios.post(url, formData, config)
                .then(response => {
                    this.$store.state.transacao.status = 'sucesso'
                    this.$store.state.transacao.mensagem = 'Registro de modelo atualizado com sucesso'

                    //limpar o campo de seleção de arquivos
                    atualizarImagem.value = ''
                
                    this.carregarLista()
                })
                .catch(errors => {
                    this.$store.state.transacao.status = 'erro'
                    this.$store.state.transacao.mensagem = errors.response.data.message
                    this.$store.state.transacao.dados = errors.response.data.errors
                })

                console.log('Atualizando modelo com ID: ' + this.$store.state.item.id);

        },
        remover() {
            let confirmacao = confirm('Tem certeza que deseja remover esse registro?')

            if (!confirmacao) {
                return false;
            }

            let formData = new FormData();
            formData.append('_method', 'delete')

            let url = this.urlBase + '/' + this.$store.state.item.id

            axios.post(url, formData)
                .then(response => {
                    this.$store.state.transacao.status = 'sucesso'
                    this.$store.state.transacao.mensagem = response.data.msg
                    this.carregarLista()
                })
                .catch(errors => {
                    this.$store.state.transacao.status = 'erro'
                    this.$store.state.transacao.mensagem = errors.response.data.erro
                })

        },
        pesquisar() {
            //console.log(this.busca)

            let filtro = ''

            for (let chave in this.busca) {

                if (this.busca[chave]) {
                    //console.log(chave, this.busca[chave])
                    if (filtro != '') {
                        filtro += ";"
                    }

                    filtro += chave + ':like:' + this.busca[chave]
                }
            }
            if (filtro != '') {
                this.urlPaginacao = 'page=1'
                this.urlFiltro = '&filtro=' + filtro
            } else {
                this.urlFiltro = ''
            }

            this.carregarLista()
        },
        paginacao(l) {
            if (l.url) {
                //this.urlBase = l.url //ajustando a url de consulta com o parâmetro de página
                this.urlPaginacao = l.url.split('?')[1]
                this.carregarLista() //requisitando novamente os dados para nossa API
            }
        },
        carregarLista() {

            let url = this.urlBase + '?' + this.urlPaginacao + this.urlFiltro
            // console.log(url)
            axios.get(url)
                .then(response => {
                    this.modelos = response.data
                    //console.log(this.modelos)
                })
                .catch(errors => {
                    console.log(errors)
                })
        },
        carregarImagem(e) {
            const fileSave = this.imgSave = e.target.files
            const file = this.arquivoImagem = e.target.files[0]
            if (file) {
                const urlImagem = URL.createObjectURL(file); // Cria uma URL temporária para o arquivo
                this.arquivoImagem = urlImagem
            }

            return fileSave;

        },
        salvar() {
            let formData = new FormData();

            formData.append('marca_id', this.marcaIDModelo);
            formData.append('nome', this.nomeModelo);
            formData.append('imagem', this.imgSave[0]);
            formData.append('numero_portas', this.numerosPortas);
            formData.append('lugares', this.lugaresModelos);
            formData.append('air_bag', this.airBagModelos);
            formData.append('abs', this.absModelos);


            let config = {
                headers: {
                    'Content-Type': 'multipart/form-data',
                }
            }

            axios.post(this.urlBase, formData, config)
                .then(response => {
                    this.transacaoStatus = 'adicionado'
                    this.transacaoDetalhes = {
                        mensagem: 'ID do registro: ' + response.data.id
                    }
                    this.carregarLista();
                    // console.log(response)
                })
                .catch(errors => {
                    this.transacaoStatus = 'erro'
                    this.transacaoDetalhes = {
                        mensagem: errors.response.data.message,
                        dados: errors.response.data.errors
                    }
                    //errors.response.data.message
                })
        }
    },
    mounted() {
        this.carregarLista()
    }
}
</script>
