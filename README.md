# Projeto de Sistemas Digitais: Métodos de Controle de Erros e Criptografia

Este repositório contém o projeto prático desenvolvido para a matéria de Sistemas Digitais. O objetivo é simular sistemas síncronos de transmissão de dados, aplicando técnicas de detecção e correção de erros utilizando ciclos de clock e armazenamento em Flip-Flops.

## 👥 Integrantes do Grupo
* [João Pedro Costa Cruz] - Responsável pelo Bit de Paridade
* [Edno Bezerra Nascimento Junior] - Responsável pelo Código de Hamming
* [Luciano Ferreira de Queiroz] - Responsável pelo CRC
* [Pedro Henrique Moura Andrade] - Responsável pelo Repetition Code

---

## 1. Bit de Paridade (Síncrono com Injeção de Erro)

### 📌 Introdução Teórica
O método do bit de paridade é uma das formas mais simples de detecção de erros em transmissões digitais. O circuito analisa um bloco de dados e adiciona um bit extra (bit de paridade), garantindo que a quantidade total de bits `1` na mensagem seja sempre PAR (Paridade Par) ou sempre ÍMPAR (Paridade Ímpar).

Ele é um método de criptografia pois empacota uma mensagem com dígitos de paridade para validação, recepção de mensagem criptografada e desempacotamento da mensagem.

Circuitos geradores de paridade dispensam a necessidade de simplificações por Mapas de Karnaugh, pois sua lógica é constituída fundamentalmente por portas XOR aninhadas em cascata para todas as suas entradas. 

### 🛠️ Implementação em Hardware (CircuitVerse)
Para garantir o comportamento síncrono exigido, o circuito foi dividido em blocos bem definidos utilizando componentes nativos do **CircuitVerse**:

1. **Registrador de Entrada:** 4 Flip-Flops D armazenam os bits de dados ($D_3, D_2, D_1, D_0$) de forma síncrona, controlados por um sinal de Clock comum gatilhado por borda de subida.
2. **Gerador combinacional:** Um subcircuito composto por portas XOR calcula a paridade ideal para as entradas armazenadas.
3. **Flip-Flop de Paridade:** Um quinto Flip-Flop D armazena o bit de paridade gerado, simulando o dado pronto para envio.

### 🧪 Cenários de Teste

#### Cenário A: Transmissão Normal (Sem Erros)
Quando o sistema opera em modo convencional, a paridade calculada pelo transmissor coincide com a checagem no receptor. A saída final calibrada resulta em `0` (Sinal Verde), indicando transmissão bem-sucedida. Feito com entradas paralelas para exemplificar como seria.

![Circuito Modo Normal](bit-de-paridade/gifs/Verificador_Comum.gif)

#### Cenário B: Teste com Injeção de Erro
Uma porta XOR intermediária foi adicionada à saída do quinto Flip-Flop, controlada por uma chave manual (`Err`). Ao ativar essa chave ($1$), forçamos a inversão do bit de paridade original, simulando um ruído no canal de comunicação. O receptor identifica instantaneamente a divergência e altera a saída final para `1` (Erro Detectado). Montado com registrador de deslocamento para deixar mais sofisticado e próximo da realidade.

![Circuito Injeção de Erro](bit-de-paridade/imagens/VerificaçãoComErro.png)

---

## 2. Transmissor de Hamming

### 📌 Introdução Teórica
O circuito transmissor de Hamming utiliza múltiplos bits de paridade para realizar a detecção de possíveis falhas ocasionadas por ruídos durante o processo de transmissão. Existem diferentes variações do circuito com a capacidade de lidar com diferentes tamanhos de entrada. No caso do transmissor Hamming (7,4), ele lida com entradas de 4 bits, acrescentando mais 3 bits de paridade ao conjunto de dados antes de realizar o envio das informações. Após receber a mensagem, novos cálculos de paridade são realizados para calcular os valores críticos ($C_1, C_2, C_3$) e determinar possíveis alterações nos dados. Caso o cálculo de todos resulte em zero, a mensagem não possui erros; do contrário, o número binário formado pelos valores críticos indica a posição exata do bit que sofreu variação.

De modo atípico, o circuito desenvolvido possui mais uma camada de segurança (criptografia) utilizando uma chave simétrica e portas XOR para inverter o valor de certos bits de entrada.

### 🛠️ Implementação em Hardware (CircuitVerse)

1. **Gerador de bits de paridade:** 3 portas XOR de 3 entradas, responsáveis por calcular a paridade de 3 subconjuntos distintos dos dados de entrada;
2. **Envio sequencial dos bits:** os valores de entrada ($D_1, D_2, D_3, D_4$) e os bits de paridade ($P_1, P_2, P_3$) alimentam um circuito multiplexador (MUX) que envia cada bit conforme o sinal de clock;
3. **Recebimento:** Um circuito demultiplexador (DEMUX) recebe os bits enviados pelo MUX e os armazena em 7 flip-flops SR;
4. **Detecção de falhas:**Os dados armazenados nos flip-flops são utilizados em um novo cálculo de paridade, por meio de portas XOR de 4 entradas, para determinar os valores críticos ($C_1, C_2, C_3$) que são armazenados em 3 flip-flops do tipo D;
5. **Correção de falhas:** Os valores críticos são enviados para conjuntos de portas AND e NOT que antecedem portas XOR ligadas a cada flip-flop de dados. O conjunto das portas AND e NOT faz com que os valores críticos sejam decodificados para acionar a porta XOR específica que realiza a inversão do dado alterado;
6. **Criptografia:** Um conjunto de 4 portas XOR, aplicado aos dados de entrada e a uma chave de criptografia, realiza a inversão de alguns bits de dados antes da transmissão. Como essa inversão ocorre antes do cálculo do Hamming (ou é aplicada de forma que o circuito a considere como o dado original), ela não é detectada como erro pelo circuito transmissor de Hamming; desse modo, os dados são transmitidos e armazenados ainda criptografados. Um segundo conjunto de portas XOR, localizado no final do circuito, realiza a descriptografia dos dados, desde que a chave utilizada seja a mesma usada no processo de criptografia.

### 🧪 Cenários de Teste

#### Cenario A: Simulação de ruido com porta NOT:
Uma porta NOT aplicada ao ultimo valor de entrada antes do circito de envio de dados mas após o calculo dos bits de paridade simula a inversão de valor resultante de um ruido durante a transmissão.

![Transmissor de Hamming](Transmissor-de-Hamming/gifs/CircuitoCompletoHamming.gif).

---

*Próximas etapas do projeto (CRC e Repetition) serão adicionadas nas seções seguintes.*
