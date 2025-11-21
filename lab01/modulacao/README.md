# Laboratório: Modulação Digital com Áudio

Hoje vocês irão experimentar como os dados digitais podem ser transmitidos através de sinais de áudio. Este é um conceito em telecomunicações que está presente em modems, telefones e muitos outros sistemas de comunicação.

> **Modem** (abreviação de *modulador-demodulador*) é um dispositivo eletrônico responsável por viabilizar a comunicação entre computadores e redes externas, como a internet. Sua função principal é converter sinais digitais, em sinais analógicos, adequados para transmissão por meios como linhas telefônicas, cabos coaxiais ou redes móveis — e fazer o processo inverso na recepção dos dados.

![alt text](figuras/Fax_modem_antigo.jpg)


📞 **Nos anos 1990 e 2000**, o modem foi um dos principais meios de acesso à internet, especialmente antes da popularização da banda larga. Naquela época, para se conectar à internet, era necessário utilizar a linha telefônica e realizar uma ligação para o provedor de acesso. 

Esse processo gerava o famoso som de conexão — uma sequência de ruídos metálicos característicos — e impedia o uso simultâneo do telefone fixo. A velocidade era bastante limitada, geralmente entre 28,8 kbps e 56 kbps, o que tornava a navegação lenta e exigia paciência.

<iframe width="640" height="480" src="https://www.youtube.com/embed/ofVEEWObx_I" title="Som da conexão discada (Dial-up)" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

[|YouTube](https://www.youtube.com/watch?v=ofVEEWObx_I)


O objetivo desse laboratório é compreender como sequências de bits (0s e 1s) podem ser convertidas em sinais audíveis e depois recuperadas no receptor. Vocês irão literalmente "ouvir" os dados sendo transmitidos!


**TOC**:
- [Como escutar os dados](#como-escutar-os-dados)
- [Setup do ambiente](#setup-do-ambiente)
- [Roteiro do Laboratório](#roteiro-do-laboratório)
    1. [Compreendendo com a Codificação/Modulação de dados](#etapa-1-compreendendo-com-a-codificaçãomodulação-de-dados)
    1. [Decodificação/Demodulação de Dados](#etapa-2-decodificaçãodemodulação-de-dados)
    1. [Impacto do Ruído na Comunicação](#etapa-3-impacto-do-ruído-na-comunicação)
    1. [Transmissão Real](#etapa-4-ruído-real)
- [Orientações de Entrega](#orientações-de-entrega)



## Como escutar os dados

Nesse exercicio, utilizaremos diferentes tons (🔉) para representar informações binárias. Esta técnica, em comunicações digitais, é conhecida como _Frequency-Shift Keying_ (FSK).

Na modulação por frequência, utilizaremos:

- **Bit 0**:
    - Representa o estado lógico baixo
    - Representado por uma frequência base de 440 Hz
    - Corresponde à nota musical Lá (A4)
    - Escolhida por ser uma frequência audível e facilmente gerada


- **Bit 1**: 
    - Representado por 880 Hz 
    - Representa o estado lógico alto
    - Exatamente uma oitava acima de 440 Hz
    - Corresponde à nota Lá (A5)

Essass frequências foram escolhidas para terem uma distância harmônica com a diferença de uma oitava (1:2) facilita a distinção entre os estados e minimiza interferências entre elas.

<!-- **Representação Matemática**
   ```
   f(0) = 440 Hz * 2^0 = 440 Hz
   f(1) = 440 Hz * 2^1 = 880 Hz
   ``` -->

> ⚠️ Recomendado utilizar taxa de transmissão baixas para facilitar as analises
> - 10 bits/segundo
> - 2 bits/segundo
> - 1 bits/segundo

### Exemplo de transmissão

Para a sequência binária `10110`:
> - 1 → 880 Hz
> - 0 → 440 Hz
> - 1 → 880 Hz
> - 1 → 880 Hz
> - 0 → 440 Hz

Visualização

```
Amplitude
  ^
  |    1   0   1   1   0
  |    ┌─┐     ┌─┐ ┌─┐
  |    | |     | | | |
  |    │ │ ┌─┐ | | | | ┌─┐
  |────┘ └─┘ └─┘ └─┘ └─┘ └───> Tempo
  |   880 440 880 880 440 Hz
```


## Setup do ambiente

<!-- Implementação dos Codificadores -->
Requisistos : 

- Computador com Windows, Linux ou macOS (não testado)
- Python 3.x com bibliotecas:
  - `numpy`
  - `scipy` 
  - `matplotlib`  
- Interface de áudio (alto‐falante ou fone + microfone)  
- Dispositivo externo (celular/tablet) para reprodução  

1. **Instale as bibliotecas necessárias:**

   ```bash
   pip install numpy matplotlib soundfile sounddevice scipy
   ```

2. **Teste o sistema de áudio** como as instruções do Setup do notebook.


### Bibliotecas Necessárias

```python
import numpy as np
import matplotlib.pyplot as plt
import soundfile as sf
import sounddevice as sd
from scipy import signal
import time

# Configurações globais
SAMPLE_RATE = 44100  # Taxa de amostragem
BIT_DURATION = 1.0   # 1 segundo por bit
FREQ_LOW = 440       # Frequência para '0' (Lá)
FREQ_HIGH = 880      # Frequência para '1' (Lá oitava)
```


<!-- 
## Parte 3: Roteiro do Laboratório

Execute as etapas observando os codigos para

**Etapa 1:** Explore esta etapa para se familiarizar com a codificação/modulação de dados

**Etapa 2:** Decodificação/Demodulação de dados

**Etapa 3:** Execute `exercicio_ruido()` para ver o efeito do ruído

**Etapa 4:** Execute `exercicio_microfone()` para a experiência completa 
-->

## Roteiro do Laboratório

Neste laboratório, você irá explorar o processo de modulação e demodulação de dados através da manipulação de frequências sonoras. Abaixo, está o roteiro detalhado para conduzir sua experiência.

Leia atentemente as instruções e utilize o [lab_notebook.ipynv 🐍](./lab_notebook.ipynb) para praticar.

### **Etapa 1**: Compreendendo com a Codificação/Modulação de dados

Nesta primeira etapa, vocês irão explorar como uma sequência de bits pode ser transformada em um sinal de áudio audível. Este processo é conhecido como modulação digital.

**O que vocês irão fazer:**

1. **Execute o primeiro código disponível** e observe o que acontece
2. **Escutem com atenção** os sons produzidos - cada tom representa um bit diferente
3. **Identifiquem os padrões**: Notem como bits diferentes produzem frequências diferentes
4. **Experimentem com diferentes sequências** de bits modificando os dados de entrada

📖  **Questões para reflexão** 
- Quantos tons diferentes vocês conseguem distinguir?
    R: Consigo distinguir dois tons, um mais grave (frequência mais baixa) e o outro, consequentemente, mais agudo (frequência maior).
- É possível identificar qual tom representa 0 e qual representa 1?
    R: Analisando o código apresentado e escutando o áudio, percebemos que o tom mais agudo é o que representa o bit 1, enquanto o mais grave representa o bit 0.
- O que acontece quando há muitos bits iguais consecutivos?
    R: Existe uma dificuldade em decodificar vários bits iguais consecutivamente.

### **Etapa 2**: Decodificação/Demodulação de Dados

Agora vocês irão trabalhar no processo inverso: como recuperar os dados originais a partir do sinal de áudio recebido. Este é o papel do receptor em um sistema de comunicação.

**O que vocês irão fazer**:
1. **Execute o segundo código** que implementa o processo de decodificação
2. **Observem como o sinal** é analisado para identificar as frequências presentes
3. **Comparem os dados** originais com os dados recuperados
4. **Testem com diferentes mensagens** para verificar a eficácia do sistema

🔎❓ **Perguntas para investigar** 
- **Q2.1** : Que fatores podem afetar a qualidade da decodificação?
    R: Acredito que depende diretamente da qualidade do sinal codificado, erros durante a transmissão do sinal ou perdas de dados a partir da compressão da informação em formatos como em MP3.
- **Q2.2** : O que acontece se a taxa de transmissão for muito alta?
    R: Isso pode melhorar a qualidade de áudio, uma vez que mais dados são usados para representar o sinal original. Por outro lado, é requerido um hardware melhor, que geralmente tende a custar mais, pois esse aumento na taxa geralmente é acompanhado pela crescente quantidade de erros na detecção dos bits.
- **Q2.3** : Como o sistema decide se a frequências representa '0' ou '1'? (observe os codigos)
    R: Se a frequência detectada for superior a 660Hz, então é o bit 1, caso contrário o bit é 0, está presente na função frequency_to_bit.

🔓🔎 **Análise de modulação**
- Busquem pelo o arquivo de audio com a sua mátricula no diretorio [dados_codificados](./dados_codificados/) e respondas as seguintes questões:
    - **A2.1** : Qual a modulação foi utilizada?
        R: A modulação utilizada foi a de Frequency-Shift Keying (FSK).
    - **A2.2** : Qual o número de bits na menssagem
        R: 41
    - **A2.3** : Qual a menssagem enviada?
        R: “10100101100101011010100110101001010110011”

> Vocês tem até 3 dias apos o labotario ser explicado para verificar se a sua menssagem esta codificada corretamente!

### **Etapa 3**: Impacto do Ruído na Comunicação

Nesta etapa, vocês irão experimentar um dos maiores desafios em comunicações reais: o ruído. Todo sistema de comunicação está sujeito a interferências que podem corromper os dados.

**O que vocês irão fazer**: Utilizando a menssagem recebida na etapa anterior.
1. **Adicionem ruido ao sinal** por meio da função específica
2. **Escutem e visualize a diferença** entre o sinal limpo e o sinal com ruído
3. **Observem como o ruído afeta** a capacidade de recuperar os dados corretamente


📖  **Questões para reflexão** 
- Existem padrões de bits mais resistentes ao ruído que outros?
    R: A própria modulação FSK por exemplo é um padrão resistente, uma vez que os receptores podem usar filtros de frequência para isolar o sinal desejado e rejeitar grande parte do ruído.
- As taxas de bits por segundo modificam a resistencia a ruídos
    R: Uma taxa de bits mais baixa permite utilizar sistemas de modulação mais simples, podendo ser redundante na transmissão do sinal e isso aumenta a resistência a ruídos. Por outro lado, uma taxa mais alta, pode não permitir redundância do sinal e isso pode ocasionar mais erros com ruídos.
- Como isso se relaciona com situações reais (telefone com interferência, rádio com estática)?
    R: Conseguimos perceber isso na ligação no momento em que a qualidade do áudio piora à medida que aumenta-se a distância ou interferência, misturando o ruído ao sinal de áudio ao mesmo tempo.

🔎❓**Análise crítica**:
- **A3.1** : Utilizando a sua menssagem da Etapa 2. A partir de que nível de ruído, para cada modulação, o sistema começa a falhar? 
    - a) Identifique o valor de SNR onde os primeiros bits são comprometidos
        R: -32
    - b) Identifique o valor de SNR onde os primeiros todos os bits são comprometidos
        R: -35
    - Utilize graficos para ilustrar os resultados onde o eixo x representa o valor de SNR e o y o número de erros

> Responda o item A3.1 como um relátorio utilize os graficos para explicar os seus achados

### **Etapa 4**: Ruído Real

Esta é a experiência mais realística do laboratório! Vocês irão simular um sistema completo de comunicação usando o microfone e alto-falantes do computador, criando um canal de comunicação real através do ar.


O que vocês irão fazer:
1. **Execute a função final** que utiliza o microfone para capturar sinais
2. **Configurem o ambiente**: Posicionem adequadamente microfone e alto-falantes
3. **Testem a transmissão** de mensagens reais através do ar
4. **Experimentem diferentes condições**: ruído ambiente

🔓 **Perguntas para investigar** 
- **Q4.1** : Qual foi a menssagem enviada enviada no arquivo `dados_ar.wav`?
    R: 10110
- **Q4.2** : Quantas reproduções (vezes) foram necessarias para decodificar a menssagem completa?
    R: 37 vezes.


## Orientações de Entrega

Para entregar as atividades do laboratório "Modulação Digital com Áudio", siga estas orientações:

* **Respostas no Google Classroom:** As respostas para as questões devem ser inseridas diretamente no formulário disponível no Google Classroom.

* **Análise A3.1 (PDF e Repositório):** Para a análise **A3.1**, você deve anexar o **PDF do relatorio** com os resultados e gráficos. Além disso, inclua o **link para o repositório** contendo o código-fonte. Ambos devem ser enviados no formulário do Google Classroom.
<!-- 
* **Análise A3.1 (PDF e Repositório):** Para a análise **A3.1**, você deve anexar o **PDF do notebook** com os resultados e gráficos. Além disso, inclua o **link para o repositório** contendo o código-fonte. Ambos devem ser enviados no formulário do Google Classroom. -->

Certifique-se de que todas as questões foram respondidas e que os arquivos e links estão corretos antes de finalizar a entrega.

