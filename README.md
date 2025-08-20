Blueprint Técnico Detalhado: Indicador Espiral Cíclica de Mercado (ECM)

Versão 7.0 (Blueprint Definitivo)

1.0 Sumário Executivo e Filosofia de Design

1.1 Declaração de Propósito

Este documento serve como o blueprint técnico completo para o desenvolvimento e a implementação do indicador Espiral Cíclica de Mercado (ECM). Ele detalha a arquitetura, os componentes algorítmicos, a lógica de cálculo, as justificativas de design e os parâmetros operacionais. O objetivo é fornecer uma especificação técnica inequívoca para garantir uma implementação fiel à sua concepção teórica.

1.2 Filosofia de Design

O ECM foi concebido para ser uma ferramenta de análise de contexto, não um gerador de sinais. Sua filosofia se baseia em três pilares fundamentais que governam cada aspecto de seu design:

* Sincronização: O indicador não impõe parâmetros fixos ao mercado. Ele primeiro mede o ritmo (ciclo) do mercado em tempo real e, em seguida, ajusta dinamicamente todos os seus cálculos para operar em harmonia com esse ritmo.

* Confluência: Nenhuma informação é analisada isoladamente. A ação do preço é constantemente qualificada pelo volume, e a análise de preço/volume é contextualizada pela análise temporal. O indicador busca a confluência de múltiplos fatores para construir um quadro de alta probabilidade.

* Probabilidade: O ECM abraça a natureza incerta do mercado. Em vez de fornecer previsões determinísticas, ele mapeia o futuro em termos de zonas de probabilidade (as bandas de volatilidade) e janelas de tempo críticas (as zonas de Fibonacci), fornecendo ao trader um mapa de possibilidades, não um caminho único.

2.0 Arquitetura do Sistema e Fluxo de Dados

O ECM opera como um sistema modular, onde cada componente executa uma tarefa específica e alimenta o próximo na cadeia de cálculo. O fluxo de dados a cada novo candle é o seguinte:

* Entrada de Dados: O sistema recebe os dados brutos do mercado (Preço e Volume).

* Motor Cíclico: Analisa a série de preços para calcular o P_ciclo (período do ciclo dominante).

* Cálculos Base: O valor de P_ciclo é distribuído para os componentes de cálculo:

* A Linha Central (VWMA) é calculada usando P_ciclo.

* O ATR é calculado usando P_ciclo.

* Cálculos Dependentes:

* O ATR Ponderado por Volume é calculado.

* As Bandas de Volatilidade são calculadas com base na VWMA e no ATR Ponderado.

* O Motor de Contexto de Volume analisa o volume em relação às bandas.

* Análise Estratégica:

* O Sistema de Confirmação de Pivô verifica se um novo ponto de ancoragem foi estabelecido.

* Se sim, as Bandas de Exaustão e o Módulo de Sincronia Temporal são reiniciados a partir da nova âncora.

* Saída Visual: Todos os componentes calculados são plotados no gráfico, incluindo as projeções futuras e os labels textuais.

3.0 Detalhamento Técnico dos Componentes

Esta seção descreve cada módulo do indicador em profundidade.

3.1 Módulo 1: O Motor Cíclico Estabilizado (A Alma de \pi)

* O que é: O coração do indicador. Um algoritmo que mede o período do ciclo dominante do mercado em tempo real.

* Como Funciona (Algoritmo):

* Obtenção da Série de Preços: Utiliza a Fonte do Preço definida pelo usuário (padrão: Fechamento).

* Aplicação da Transformada de Hilbert: Sobre a série de preços, aplica-se a Transformada de Hilbert para obter a Fase Instantânea de cada candle. A fase é um ângulo que descreve onde o preço está em seu ciclo atual.

* Cálculo do Período "Cru": O período do ciclo é derivado da taxa de variação da fase entre um candle e o anterior. O resultado é uma série de valores de período, um para cada candle, que é inerentemente volátil.

* Filtro de Estabilização: Sobre esta série de períodos "crus", aplica-se uma Média Móvel Exponencial (EMA) com o Período do Filtro Cíclico (padrão: 3).

* Saída: O resultado final é o P_ciclo, um valor de período suavizado e estável que representa o ritmo atual do mercado.

* Por que foi feito assim?

* A Transformada de Hilbert é o padrão ouro em processamento de sinais para medir ciclos em dados não estacionários como o mercado. No entanto, sua aplicação em tempo real sofre do "end effect", causando instabilidade no último valor. O filtro de EMA é a solução de engenharia para este problema: ele remove o ruído e a instabilidade com um custo mínimo de lag, tornando a medição robusta e confiável para ser usada pelos outros componentes.

3.2 Módulo 2: A Linha Central Sincronizada (O Eixo Preditivo)

* O que é: A linha de tendência central do indicador, que serve como base para todos os outros cálculos de preço e como eixo para a projeção futura.

* Como Funciona (Algoritmo):

* Cálculo da VWMA: Calcula-se uma Média Móvel Ponderada por Volume (VWMA) usando a Fonte do Preço. O período N desta VWMA é sempre o valor atual de P_ciclo.

* Cálculo da Inclinação (Slope): Calcula-se a inclinação do último segmento da linha da VWMA (a diferença de valor entre o candle atual e o anterior).

* Projeção Futura: A partir do valor da VWMA no candle atual, projeta-se uma linha reta para o futuro por N candles (Período da Projeção), usando a inclinação calculada.

* Por que foi feito assim?

* A VWMA foi escolhida sobre a SMA ou EMA porque ela incorpora a convicção do mercado (volume), representando o "preço justo" de forma mais precisa. Usar o P_ciclo como período garante que a sensibilidade da linha de tendência esteja sempre sincronizada com o ritmo do mercado, tornando-a mais rápida em mercados voláteis e mais lenta em mercados consolidados. A projeção linear é a representação mais honesta da inércia do mercado.

3.3 Módulo 3: O Envelope de Volatilidade Confirmada (A Expansão Tática de \phi)

* O que é: As bandas internas que mapeiam as zonas de energia e volatilidade de curto prazo.

* Como Funciona (Algoritmo):

* Cálculo do ATR Base: Calcula-se o Average True Range (ATR) padrão, usando o P_ciclo como período.

* Cálculo do Fator de Volume: Calcula-se um fator de qualificação de volume. Uma abordagem simples é: Fator Vol = Volume do Candle Atual / Média Móvel Simples do Volume dos últimos P_ciclo períodos.

* Cálculo do ATR Ponderado: ATR_Ponderado = ATR_Base * Fator_Vol.

* Cálculo das Bandas: As bandas são plotadas acima e abaixo da Linha Central (VWMA), incluindo sua projeção. A fórmula é: Banda = VWMA ± (Multiplicador * ATR_Ponderado). Os Multiplicadores são os valores baseados em \phi (1.0, 1.618, 2.618).

* Por que foi feito assim?

* O ATR padrão trata toda a volatilidade como igual. Um candle longo com baixo volume (possivelmente ruído em baixa liquidez) teria o mesmo impacto que um candle longo com volume massivo (um rompimento verdadeiro). Ao ponderar o ATR pelo volume, o indicador filtra o ruído e foca nas expansões de volatilidade que são confirmadas pela participação do mercado, tornando as bandas mais significativas. Os multiplicadores de \phi são usados com base na teoria de que os mercados se movem em proporções harmônicas.

3.4 Módulo 4: O Envelope de Exaustão Ancorado (A Expansão Estratégica de \phi)

* O que é: O par de bandas mais externo, que modela o ciclo de vida da tendência principal e validada.

* Como Funciona (Algoritmo):

* Detecção de Pivô (Trigger): O sistema está constantemente procurando por um novo pivô de ancoragem.

* Sistema de Confirmação Tripla: Um novo pivô (ex: um fundo) só é confirmado e se torna a nova âncora se todas as três condições a seguir forem verdadeiras:

* Preço: O preço fez uma nova mínima em um longo período de lookback (definido pelo Fator de Lookback do Pivô * P_ciclo).

* Tendência: A inclinação da Linha Central (VWMA) virou de negativa para positiva.

* Ciclo: O P_ciclo mostrou um comportamento de "reset" (ex: alongou-se indicando fadiga e depois encurtou-se indicando nova aceleração).

* Cálculo da Curva: Uma vez ancorada, a banda é calculada a cada candle com uma fórmula de crescimento exponencial: Banda = Ponto_de_Ancora ± A * exp(B * t), onde t é o número de candles desde a âncora, A é uma constante inicial (pode ser o valor do ATR no ponto da âncora) e B é a taxa de crescimento, que é dinamicamente influenciada pela inclinação média da VWMA desde a âncora.

* Por que foi feito assim?

* Esta é a defesa mais forte contra a instabilidade. Ancorar em qualquer fundo ou topo criaria um indicador errático. A confirmação tripla garante que as bandas estratégicas só sejam redesenhadas quando há evidência confluente de uma mudança de regime no mercado. Isso torna a análise de exaustão extremamente robusta e confiável, representando o ciclo de vida da tendência real, não de movimentos aleatórios.

3.5 Módulo 5: O Motor de Contexto de Volume (A Convicção)

* O que é: Um sistema de classificação que dá significado ao volume com base em sua localização.

* Como Funciona (Algoritmo):

* Detecção de Volume Anômalo: A cada candle, verifica-se se o volume está acima de um limiar (ex: Média de Volume * Fator de Volume Climático).

* Análise de Localização: Se o volume for anômalo, verifica-se a posição do preço do candle:

* Se o preço está próximo à Linha Central (VWMA), o volume é classificado como "Ignição".

* Se o preço está tocando ou excedendo as bandas externas (especialmente a Banda de Exaustão), o volume é classificado como "Exaustão".

* Saída: A classificação é usada internamente para qualificar os eventos do indicador.

* Por que foi feito assim?

* Resolve a ambiguidade fundamental do volume. Em vez de uma leitura binária, o indicador ganha uma camada de interpretação comportamental, permitindo ao trader diferenciar entre o volume que inicia uma tendência e o volume que a termina.

3.6 Módulo 6: O Módulo de Sincronia Temporal (O "Quando")

* O que é: Um motor de projeção temporal que identifica janelas de tempo de alta probabilidade para eventos de mercado.

* Como Funciona (Algoritmo):

* Ancoragem: Utiliza os mesmos pivôs confirmados pelo Módulo 4 como âncoras temporais.

* Medição da Unidade de Tempo (UT): Mede o número de candles entre a âncora anterior e a âncora atual.

* Projeção das Janelas: A partir da âncora atual, projeta zonas verticais sombreadas centradas nos múltiplos de Fibonacci da UT (ex: Ancora + UT * 1.618).

* Cálculo da Largura Variável: A largura da janela em candles é uma função crescente da distância da projeção (ex: 3 candles para a primeira, 5 para a segunda, etc.), refletindo a crescente incerteza.

* Cálculo da Relevância: A relevância de cada janela projetada é calculada com base na "qualidade" do movimento que gerou a UT (média da inclinação da VWMA e média do volume durante aquele período).

* Plotagem do Label Textual: Acima de cada janela, um label é plotado com o nível Fibonacci e a relevância calculada (ex: [ 1.618 | Relevância: Alta ]).

* Por que foi feito assim?

* Abraça a natureza probabilística da análise temporal. Substituir linhas por janelas e adicionar um indicador de relevância é uma representação mais honesta e útil. O trader sabe não apenas "quando" olhar, mas também "quão importante" é aquela janela de tempo e qual a sua "margem de erro". Os labels textuais garantem total acessibilidade.

4.0 Parâmetros de Calibração Fina

Estes são os únicos parâmetros ajustáveis pelo usuário. Eles não controlam os períodos (que são automáticos), mas sim as constantes teóricas do modelo.

* Fonte do Preço: (Padrão: Fechamento) - Define qual preço usar nos cálculos.

* Multiplicadores das Bandas: (Padrão: 1.0, 1.618, 2.618) - Permite customizar os níveis de energia de \phi.

* Período do Filtro Cíclico: (Padrão: 3) - Controla a suavização do motor de ciclo. Valores maiores criam um ciclo mais estável, mas com mais lag.

* Fator de Volume Climático: (Padrão: 2.0) - Define o que é considerado um volume "anômalo" (2.0 = 100% acima da média).

* Fator de Lookback do Pivô: (Padrão: 3.0) - Controla a magnitude do movimento necessário para definir uma nova âncora estratégica.
