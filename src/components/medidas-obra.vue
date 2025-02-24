<template>
  <div>
    <!-- Header -->
    <v-toolbar color="#313030" dark density="compact">
      <v-img 
        src="/logo.png"
        max-height="40" 
        max-width="40"       
      />
      <v-toolbar-title class="texto-amarelo">na Obra</v-toolbar-title>
      <v-spacer></v-spacer>

      <!-- Menu de três pontinhos -->
      <v-menu>
        <template v-slot:activator="{ props }">
          <v-btn
            icon
            v-bind="props"
            class="ml-2"
          >
            <v-icon class="texto-amarelo">mdi-dots-vertical</v-icon>
          </v-btn>
        </template>

        <!-- Itens do menu -->
        <v-list variant="tonal" color="background" class="pa-0">
          <v-list-item 
            @click="adicionarComodo"
            class="ma-2"
          >
            <v-list-item-title>Adicionar Cômodo</v-list-item-title>
          </v-list-item>
          <v-list-item 
            @click="confirmarLimparDados"
            class="ma-2 text-error"
          >
            <v-list-item-title>Limpar Todos os Dados</v-list-item-title>
          </v-list-item>
        </v-list>
      </v-menu>
    </v-toolbar>

    <!-- Tabs -->
    <v-tabs v-model="abaAtiva" background-color="primary" dark>
      <v-tab value="obra">Obra</v-tab>
      <v-tab value="relatorio">Relatório</v-tab>
      <v-tab value="produtos">Produtos</v-tab>
      <v-tab value="faleconosco">Fale Conosco</v-tab>
    </v-tabs>

    <v-window v-model="abaAtiva">
      <!-- Aba Obra -->
      <v-window-item value="obra">
        <div class="container-comodos pa-4">
          <div v-for="(comodo, index) in comodos" :key="index" class="mb-6">
            <v-card variant="tonal" class="mx-auto" style="max-width: 800px;">
              <v-card-title class="d-flex align-center py-3">
                <v-text-field
                  v-model="comodo.nomeComodo"
                  label="Nome do Cômodo"
                  variant="outlined"
                  density="compact"
                  class="mr-2"
                  hide-details
                ></v-text-field>
                <v-btn
                  icon="mdi-delete"
                  variant="text"
                  color="error"
                  @click="confirmarRemocao('comodo', index)"
                ></v-btn>
              </v-card-title>

              <v-card-text>
                <!-- Seção de Medidas -->
                <div class="mb-6">
                  <div class="d-flex justify-space-between align-center mb-2">
                    <h3 class="text-h6">Medidas</h3>
                    <v-btn
                      size="small"
                      variant="tonal"
                      @click="adicionarMedida(comodo)"
                    >
                      Adicionar Medida
                    </v-btn>
                  </div>
                  <v-table density="compact">
                    <thead>
                      <tr>
                        <th width="50"></th>
                        <th>Altura (m)</th>
                        <th>Largura (m)</th>
                        <th>Área (m²)</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="(medida, medidaIndex) in comodo.medidas" :key="medidaIndex">
                        <td>
                          <v-btn
                            icon="mdi-delete"
                            variant="text"
                            color="error"
                            size="x-small"
                            @click="confirmarRemocao('medida', { comodo, medidaIndex })"
                          ></v-btn>
                        </td>
                        <td>
                          <v-text-field
                            v-model.number="medida.altura"
                            class="input-numero"
                            type="number"
                            min="0"
                            step="0.01"
                            density="compact"
                            hide-details
                          ></v-text-field>
                        </td>
                        <td>
                          <v-text-field
                            v-model.number="medida.largura"
                            class="input-numero"
                            type="number"
                            min="0"
                            step="0.01"
                            density="compact"
                            hide-details
                          ></v-text-field>
                        </td>
                        <td class="text-medium-emphasis">
                          {{ calcularResultado(medida) }}
                        </td>
                      </tr>
                    </tbody>
                  </v-table>
                </div>

                <!-- Seção de Recortes -->
                <div>
                  <div class="d-flex justify-space-between align-center mb-2">
                    <h3 class="text-h6 text-error">Recortes</h3>
                    <v-btn
                      size="small"
                      variant="tonal"
                      color="error"
                      @click="adicionarRecorte(comodo)"
                    >
                      Adicionar Recorte
                    </v-btn>
                  </div>
                  <v-table density="compact">
                    <thead>
                      <tr>
                        <th width="50"></th>
                        <th>Altura (m)</th>
                        <th>Largura (m)</th>
                        <th>Qtd itens</th>
                        <th>Área Total (m²)</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="(recorte, recorteIndex) in comodo.recortes" :key="recorteIndex">
                        <td>
                          <v-btn
                            icon="mdi-delete"
                            variant="text"
                            color="error"
                            size="x-small"
                            @click="confirmarRemocao('recorte', { comodo, recorteIndex })"
                          ></v-btn>
                        </td>
                        <td>
                          <v-text-field
                            v-model.number="recorte.altura"
                            class="input-numero"
                            type="number"
                            min="0"
                            step="0.01"
                            density="compact"
                            hide-details
                            color="error"
                          ></v-text-field>
                        </td>
                        <td>
                          <v-text-field
                            v-model.number="recorte.largura"
                            class="input-numero"
                            type="number"
                            min="0"
                            step="0.01"
                            density="compact"
                            hide-details
                            color="error"
                          ></v-text-field>
                        </td>
                        <td>
                          <v-text-field
                            v-model.number="recorte.quantidade"
                            style="max-width: 150px;"
                            type="number"
                            min="1"
                            density="compact"
                            hide-details
                            color="error"
                          ></v-text-field>
                        </td>
                        <td class="text-error">
                          -{{ calcularResultadoRecorte(recorte) }}
                        </td>
                      </tr>
                    </tbody>
                  </v-table>
                </div>

                <!-- Total do Cômodo -->
                <v-divider class="my-4"></v-divider>
                <div class="text-right text-caption font-weight-medium">
                  Total {{ comodo.nomeComodo || '[Sem nome]' }}: 
                  {{ calcularAreaComodo(comodo) }} m²
                </div>
              </v-card-text>
            </v-card>
          </div>

          <!-- Botão Adicionar Cômodo -->
          <div class="text-center mt-4">
            <v-btn
              color="#313030"
              variant="tonal"
              @click="adicionarComodo"
            >
              Adicionar Cômodo
            </v-btn>
          </div>

          <!-- Total Geral -->
          <v-footer class="bg-grey-lighten-3 mt-8 py-3 text-center">
            <v-spacer></v-spacer>
            <span class="text-body-2 font-weight-bold">
              Total de todas as áreas: {{ calcularAreaTotal() }} m²
            </span>
            <v-spacer></v-spacer>
          </v-footer>
        </div>
      </v-window-item>

      <!-- Aba Relatório -->
      <v-window-item value="relatorio">
        <div class="pa-4">
          <!-- Tabela do Relatório -->
          <div id="relatorioContent" class="mb-6">
            <v-card variant="tonal" class="mb-6">
        <v-card-title class="text-h6">Relatório de Medidas</v-card-title>
        <v-divider></v-divider>
        <v-card-text>
          <v-table>
            <thead>
              <tr>
                <th width="50"></th>
                <th width="50">
                  <v-checkbox
                    v-model="allSelected"
                    hide-details
                    density="compact"
                  ></v-checkbox>
                </th>
                <th>Cômodo</th>
                <th>Medidas (m²)</th>
                <th>Descontos (m²)</th>
                <th>Total (m²)</th>
              </tr>
            </thead>
            <tbody>
              <template v-for="(comodo, index) in comodos" :key="index">
                <tr>
                  <td>
                    <v-btn
                      icon
                      @click="toggleExpansao(index)"
                      size="x-small"
                      variant="text"
                    >
                      <v-icon>
                        {{ comodosExpandidos.includes(index) ? 'mdi-chevron-up' : 'mdi-chevron-down' }}
                      </v-icon>
                    </v-btn>
                  </td>
                  <td>
                    <v-checkbox
                      v-model="selectedComodos"
                      :value="index"
                      hide-details
                      density="compact"
                    ></v-checkbox>
                  </td>
                  <td>{{ comodo.nomeComodo || '[Sem nome]' }}</td>
                  <td>{{ calcularAreaMedidas(comodo) }}</td>
                  <td class="text-error">{{ calcularAreaDescontos(comodo) }}</td>
                  <td class="font-weight-medium">{{ calcularAreaComodo(comodo) }}</td>
                </tr>
                
                <!-- Linha expandida -->
                <tr v-if="comodosExpandidos.includes(index)">
                  <td colspan="6" class="pa-0">
                    <v-expand-transition>
                      <div v-show="comodosExpandidos.includes(index)" class="detalhes-comodo">
                        <div class="pa-4">
                          <div class="ml-8">
                            <h4 class="text-subtitle-1 mb-2">Detalhes das Medidas:</h4>
                            <v-table density="compact" class="mb-4">
                              <thead>
                                <tr>
                                  <th>Altura (m)</th>
                                  <th>Largura (m)</th>
                                  <th>Área (m²)</th>
                                </tr>
                              </thead>
                              <tbody>
                                <tr v-for="(medida, mIndex) in comodo.medidas" :key="mIndex">
                                  <td>{{ medida.altura || 0 }}</td>
                                  <td>{{ medida.largura || 0 }}</td>
                                  <td>{{ calcularResultado(medida) }}</td>
                                </tr>
                              </tbody>
                            </v-table>

                            <h4 class="text-subtitle-1 mb-2">Detalhes dos Recortes:</h4>
                            <v-table density="compact">
                              <thead>
                                <tr>
                                  <th>Altura (m)</th>
                                  <th>Largura (m)</th>
                                  <th>Quantidade</th>
                                  <th>Área Total (m²)</th>
                                </tr>
                              </thead>
                              <tbody>
                                <tr v-for="(recorte, rIndex) in comodo.recortes" :key="rIndex">
                                  <td>{{ recorte.altura || 0 }}</td>
                                  <td>{{ recorte.largura || 0 }}</td>
                                  <td>{{ recorte.quantidade || 1 }}</td>
                                  <td class="text-error">-{{ calcularResultadoRecorte(recorte) }}</td>
                                </tr>
                              </tbody>
                            </v-table>
                          </div>
                        </div>
                      </div>
                    </v-expand-transition>
                  </td>
                </tr>
              </template>
            </tbody>
          </v-table>
        </v-card-text>
      </v-card>


            <!-- Total Selecionado -->
            <v-card variant="tonal" class="mb-6">
              <v-card-text class="text-right text-body-1 font-weight-bold">
                Total Selecionado: {{ metragem }} m²
              </v-card-text>
            </v-card>

            <!-- Total Selecionado -->
            <v-card variant="tonal" class="mb-6">
              <v-card-text class="text-right text-body-1 font-weight-bold">
                Total de todas as áreas: {{ calcularAreaTotal() }} m²
              </v-card-text>
            </v-card>

          </div>

          <!-- Ações do Relatório -->
          <div class="d-flex flex-wrap ga-2 justify-center">
            <v-btn
              color="#f2cb1c"
              variant="tonal"
              @click="gerarPDF"
            >
              <v-icon start>mdi-file-pdf-box</v-icon>
              Salvar PDF
            </v-btn>
            
            <v-btn
              color="success"
              variant="tonal"
              @click="enviarWhatsApp"
            >
              <v-icon start>mdi-whatsapp</v-icon>
              Pedir Orçamento
            </v-btn>
          </div>

          <!-- Calculadora -->
          <v-card variant="tonal" class="mt-8 mx-auto" style="max-width: 600px;">
            <v-card-title class="text-h6">Calculadora de Material</v-card-title>
            <v-divider></v-divider>
            <v-card-text>
              <v-row>
                <v-col cols="12" sm="6">
                  <v-text-field
                    v-model.number="metragem"
                    label="Metragem total (m²)"
                    type="number"
                    min="0"
                    step="0.01"
                    variant="outlined"
                    density="compact"
                  ></v-text-field>
                </v-col>
                <v-col cols="12" sm="6">
                  <v-text-field
                    v-model.number="consumoMedio"
                    label="Consumo médio (m²/un)"
                    type="number"
                    min="0.01"
                    step="0.01"
                    variant="outlined"
                    density="compact"
                  ></v-text-field>
                </v-col>
              </v-row>
              
              <div class="text-center text-h6">
                Quantidade necessária: 
                <span>{{ resultadoCalculo + ' m²/un' || '0.00' }}</span>
              </div>
            </v-card-text>
          </v-card>
        </div>
      </v-window-item>

      <!-- Aba Produtos -->
      <v-window-item value="produtos">
        <div class="pa-4">
          <v-card 
            variant="tonal" 
            class="mx-auto" 
            style="max-width: 800px"
            >
            <v-card-title class="text-h6">PRODUTOS ECOCRISTAL</v-card-title>
            <v-divider></v-divider>
            <v-card-text>
              <v-table style="position: relative;">
                <thead>
                  <tr>
                    <th>Produto</th>
                    <th>Consumo Médio (m²/un)</th>                    
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="(produto, index) in produtos" :key="index">
                    <td>{{ produto.nome }}</td>
                    <td>{{ produto.consumoMedio }}</td>                    
                  </tr>
                </tbody>
                <img
                  src="/logo.png"
                  style="
                    position: absolute;
                    top: 50%; /* Centraliza verticalmente */
                    left: 50%; /* Centraliza horizontalmente */
                    transform: translate(-50%, -50%); /* Ajusta o posicionamento */
                    max-width: 50%; /* Define a largura máxima da imagem */
                    opacity: 0.3; /* Define a opacidade da imagem */
                  "
                />
              </v-table>
            </v-card-text>
          </v-card>
        </div>
      </v-window-item>

      <!-- Conteúdo da aba Fale Conosco -->
      <v-window-item value="faleconosco"> 
        <v-container>
    <v-row justify="center">
      <v-col cols="12" md="6">
        <v-card class="text-center pa-6" variant="tonal">
          <h2 class="headline font-weight-regular mb-5">
            Entre em contato
          </h2>
          <h3 class="font-weight-light mb-2">
            Seja com dúvidas, para pedir orçamento ou enviar sugestões.            
          </h3>
          <h3 class="font-weight-light">
            Nossa equipe está aqui para ajudar!
          </h3>
          <v-card-actions class="justify-center">
            <!-- Botão para enviar mensagem no WhatsApp -->
            <v-btn
              color="green darken-1"
              dark
              href="https://wa.me/5516991731152"
              target="_blank"
              rel="noopener"
            >
              <v-icon left>mdi-whatsapp</v-icon>
              Enviar Mensagem
            </v-btn>             
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
      </v-window-item>

    </v-window>

    <!-- Diálogo de Confirmação -->
    <v-dialog v-model="dialogConfirmacao" max-width="400">
      <v-card>
        <v-card-title class="text-h6">Confirmar ação</v-card-title>
        <v-card-text>{{ mensagemConfirmacao }}</v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn variant="text" @click="cancelarRemocao">Cancelar</v-btn>
          <v-btn color="error" variant="tonal" @click="executarRemocao">Confirmar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import jsPDF from 'jspdf';
import html2canvas from 'html2canvas';

export default {
  data: () => ({        
    abaAtiva: 'obra',
    comodos: [],
    dialogConfirmacao: false,
    mensagemConfirmacao: "",
    acaoRemocao: null,
    selectedComodos: [],  
    comodosExpandidos: [], // Índices dos cômodos expandidos  
    metragem: "",
    consumoMedio: "",
    produtos: [
      { nome: "Produto 1", consumoMedio: 3.5 },
      { nome: "Produto 2", consumoMedio: 0.3 },
      { nome: "Produto 1", consumoMedio: 3.5 },
      { nome: "Produto 2", consumoMedio: 0.3 },
      { nome: "Fundo Preparador de Parede", consumoMedio: 3.5 },
      { nome: "Produto 2", consumoMedio: 0.3 },
      { nome: "Produto 1", consumoMedio: 3.5 },
      { nome: "Produto 2", consumoMedio: 0.3 },
      { nome: "Produto 1", consumoMedio: 3.5 },
      { nome: "Produto 2", consumoMedio: 0.3 },
      { nome: "Produto 1", consumoMedio: 3.5 },
      { nome: "Produto 2", consumoMedio: 0.3 },
    ],
    selecionarTodos: true, // Flag para manter todos os cômodos selecionados
  }),
  computed: {     
    allSelected: {
      get() {
        if (this.comodos.length === 0) return true;
        return this.selecionarTodos || this.selectedComodos.length === this.comodos.length;
      },
      set(value) {
        this.selecionarTodos = value;
        this.selectedComodos = value ? this.comodos.map((_, index) => index) : [];
      }
    },
    resultadoCalculo() {
      if (!this.metragem || !this.consumoMedio) return "";
      const m = parseFloat(this.metragem);
      const c = parseFloat(this.consumoMedio);
      if (isNaN(m) || isNaN(c) || c === 0) return "";
      return (m / c).toFixed(1);
    }
  },
  watch: {
    selectedComodos(newVal) {
      let soma = 0;
      newVal.forEach(index => {
        soma += parseFloat(this.calcularAreaComodo(this.comodos[index]));
      });
      this.metragem = soma ? soma.toFixed(2) : "";
    },
    comodos: {
      deep: true,
      handler() {
        this.salvarEstadoLocal();
        this.calcularMetragem();
      }
    },
    abaAtiva() {
      this.salvarEstadoLocal();
    },
    metragem() {
      this.salvarEstadoLocal();
    },
    consumoMedio() {
      this.salvarEstadoLocal();
    }
  },
  methods: {
    limparDados() {
      localStorage.removeItem('medidasObraState');
      this.comodos = [];
      this.metragem = '';
      this.consumoMedio = '';
      this.selectedComodos = [];
    },
    toggleExpansao(index) {
      const position = this.comodosExpandidos.indexOf(index)
      if (position > -1) {
        this.comodosExpandidos.splice(position, 1)
      } else {
        this.comodosExpandidos.push(index)
      }
    },
    adicionarComodo() {
      this.comodos.push({
        nomeComodo: '',
        medidas: [],
        recortes: [],
      });
      // Se a flag estiver ativa, seleciona todos os índices
      if (this.selecionarTodos) {
        this.selectedComodos = this.comodos.map((_, index) => index);
      }
      this.$nextTick(() => {
        this.calcularMetragem();
      });
    },
    calcularMetragem() {
      let soma = 0;
      this.selectedComodos.forEach(index => {
        soma += parseFloat(this.calcularAreaComodo(this.comodos[index]));
      });
      this.metragem = soma ? soma.toFixed(2) : "";
    },
    confirmarLimparDados() {
      this.mensagemConfirmacao = "Tem certeza que deseja limpar todos os dados?";
      // Vincula o método limparDados para manter o contexto
      this.acaoRemocao = this.limparDados.bind(this);
      this.dialogConfirmacao = true;
    },
    confirmarRemocao(tipo, item) {
      if (tipo === 'comodo') {
        this.mensagemConfirmacao = "Tem certeza que deseja remover este cômodo?";
        this.acaoRemocao = () => this.comodos.splice(item, 1);
      } else if (tipo === 'medida') {
        this.mensagemConfirmacao = "Tem certeza que deseja remover esta medida?";
        this.acaoRemocao = () => item.comodo.medidas.splice(item.medidaIndex, 1);
      } else if (tipo === 'recorte') {
        this.mensagemConfirmacao = "Tem certeza que deseja remover este recorte?";
        this.acaoRemocao = () => item.comodo.recortes.splice(item.recorteIndex, 1);
      }
      this.dialogConfirmacao = true;
    },
    adicionarMedida(comodo) {
      comodo.medidas.push({ altura: null, largura: null });
    },
    adicionarRecorte(comodo) {
      comodo.recortes.push({ altura: null, largura: null, quantidade: 1 });
    },
    calcularResultado(medida) {
      const altura = parseFloat(medida.altura) || 0;
      const largura = parseFloat(medida.largura) || 0;
      return (altura * largura).toFixed(2);
    },
    calcularResultadoRecorte(recorte) {
      const altura = parseFloat(recorte.altura) || 0;
      const largura = parseFloat(recorte.largura) || 0;
      const quantidade = parseInt(recorte.quantidade) || 1;
      return (altura * largura * quantidade).toFixed(2);
    },
    calcularAreaComodo(comodo) {
      const areaMedidas = comodo.medidas.reduce(
        (acc, medida) => acc + parseFloat(this.calcularResultado(medida)),
        0
      );
      const areaRecortes = comodo.recortes.reduce(
        (acc, recorte) =>
          acc + parseFloat(this.calcularResultadoRecorte(recorte)),
        0
      );
      return (areaMedidas - areaRecortes).toFixed(2);
    },
    calcularAreaMedidas(comodo) {
      return comodo.medidas
        .reduce((acc, medida) => {
          const altura = parseFloat(medida.altura) || 0;
          const largura = parseFloat(medida.largura) || 0;
          return acc + (altura * largura);
        }, 0)
        .toFixed(2);
    },
    calcularAreaDescontos(comodo) {
      return comodo.recortes
        .reduce((acc, recorte) => {
          const altura = parseFloat(recorte.altura) || 0;
          const largura = parseFloat(recorte.largura) || 0;
          const quantidade = parseInt(recorte.quantidade) || 1;
          return acc + altura * largura * quantidade;
        }, 0)
        .toFixed(2);
    },
    calcularAreaTotal() {
      return this.comodos
        .reduce(
          (total, comodo) => total + parseFloat(this.calcularAreaComodo(comodo)),
          0
        )
        .toFixed(2);
    },
    executarRemocao() {
      if (this.acaoRemocao) {
        this.acaoRemocao();
        this.dialogConfirmacao = false;
      }
    },
    cancelarRemocao() {
      this.dialogConfirmacao = false;
      this.acaoRemocao = null;
    },
    async gerarPDF() {
      const element = document.getElementById('relatorioContent');
      const canvas = await html2canvas(element, { scale: 2 });
      const imgData = canvas.toDataURL('image/png');
      
      const pdf = new jsPDF('p', 'mm', 'a4');
      const pageWidth = pdf.internal.pageSize.getWidth();
      const pageHeight = pdf.internal.pageSize.getHeight();
      
      const imgProps = pdf.getImageProperties(imgData);
      const imgHeight = (imgProps.height * pageWidth) / imgProps.width;
      
      let heightLeft = imgHeight;
      let position = 0;

      pdf.addImage(imgData, 'PNG', 0, position, pageWidth, imgHeight);
      heightLeft -= pageHeight;

      while (heightLeft >= 0) {
        position = heightLeft - imgHeight;
        pdf.addPage();
        pdf.addImage(imgData, 'PNG', 0, position, pageWidth, imgHeight);
        heightLeft -= pageHeight;
      }

      pdf.save('relatorio-obra.pdf');
    },

    enviarWhatsApp() {
      const numero = '5516991731152';
      const texto = encodeURIComponent(
        `Olá, gostaria de solicitar um orçamento.\n\n` +
        `*Metragens:*\n` +
        `📐 *Total da Obra:* ${this.calcularAreaTotal()} m²\n` +
        `📏 *Selecionada:* ${this.metragem} m²\n\n` +
        `📋 *Detalhes dos Cômodos:*\n${
          this.selectedComodos.map(index => {
            const c = this.comodos[index];
            return `• ${c.nomeComodo || '[Sem nome]'}: ${this.calcularAreaComodo(c)} m²`;
          }).join('\n')
        }`
      );
      window.open(`https://wa.me/${numero}?text=${texto}`, '_blank');
    },
    salvarEstadoLocal() {
    localStorage.setItem('medidasObraState', JSON.stringify({
      comodos: this.comodos,
      abaAtiva: this.abaAtiva,
      metragem: this.metragem,
      consumoMedio: this.consumoMedio
    }));
    },
  },
  mounted() {
    const savedState = localStorage.getItem('medidasObraState');
    if (savedState) {
      try {
        const state = JSON.parse(savedState);
        this.comodos = state.comodos || [];
        this.abaAtiva = state.abaAtiva || 'obra';
        this.metragem = state.metragem || '';
        this.consumoMedio = state.consumoMedio || '';
        // Se a flag estiver ativa, seleciona todos os índices dos cômodos
        this.selectedComodos = this.selecionarTodos ? this.comodos.map((_, index) => index) : [];
      } catch (e) {
        localStorage.removeItem('medidasObraState');
      }
    } else {
      // Inicialmente, se não houver dados salvos, podemos deixar a seleção vazia
      this.selectedComodos = [];
    }
  }
};
</script>

<style>
.v-table td, .v-table th {
    padding: 2px !important;   
}
.texto-amarelo{
  color: #f2cb1c;
}
.texto-vermelho {
  color: red;
}
footer {
  position: fixed;
  bottom: 0;
  width: 100%;
  padding: 10px;
  background-color: #f0f0f0;
}
.input-pequeno {  
  max-width: 200px;
  font-size: 14px;
}
.input-numero {
  min-width: 70px;
  max-width: 100px;
  font-size: 14px;
}
/* Remover botões de incremento nos navegadores WebKit */
input[type=number]::-webkit-inner-spin-button,
input[type=number]::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
/* Remover a aparência padrão do Firefox */
input[type=number] {
  -moz-appearance: textfield;
}
.detalhes-comodo {
  background-color: rgba(0, 0, 0, 0.03);
  border-top: 1px solid #ddd;
  border-bottom: 2px solid #ddd;
}

.detalhes-comodo h4 {
  color: #777;
  font-weight: 500;
}
</style>