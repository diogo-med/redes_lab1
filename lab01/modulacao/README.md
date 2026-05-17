# Laboratório — Modulação Digital com Áudio

**Disciplina:** Redes de Computadores — Ciência da Computação  

Dados digitais, sequências de `0`s e `1`s, podem trafegar por um meio físico analógico, como o ar ou uma linha telefônica?



[![Assista no YouTube](https://img.youtube.com/vi/PDE9b5iU8vI/0.jpg)](https://www.youtube.com/watch?v=PDE9b5iU8vI)


Neste laboratório, você irá responder a essa pergunta de forma prática: construindo, do zero, um sistema capaz de **transmitir mensagens de texto por meio de sons audíveis**. A técnica empregada, denominada _Frequency-Shift Keying_ (FSK), está na base dos modems das décadas de 1980 e 1990 — e você irá literalmente **ouvi-la funcionando**.


- [Conceitos](#conceitos)
  - [Modulações](#modulações)
  - [Métricas](#metricas)
- [Estrutura das Entregas](#estrutura-das-entregas)
  - [Entrega 1 — Relatório de Ruído](#entrega-1--relatório-de-ruído-pré-lab)
  - [Entrega 2 — Sistema de Chat por Áudio](#entrega-2--sistema-de-chat-por-áudio-dia-do-lab)
- [Orientações de Entrega](#orientações-de-entrega)


## Conceitos 

Neste laboratório, dois tons musicais representam os estados binários:

| Bit | Frequência | Nota Musical |
|:---:|:----------:|:------------:|
| `0` | 440 Hz | Lá (A4) |
| `1` | 880 Hz | Lá (A5) |

A distância de uma oitava (razão 1:2) foi escolhida por maximizar a distinção entre os tons e minimizar interferência harmônica entre eles.

Desse modo, para transmiir a sequência binária `10110` teriamos:

> - 1 → 880 Hz
> - 0 → 440 Hz
> - 1 → 880 Hz
> - 1 → 880 Hz
> - 0 → 440 Hz

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

### Modulações

**NRZ — Non-Return-to-Zero**  
Cada bit é representado por uma frequência constante durante todo o período de bit. É a modulação mais simples: `'1'` → 880 Hz, `'0'` → 440 Hz. Sua principal limitação está em longas sequências de bits idênticos, que dificultam a sincronização entre transmissor e receptor.

**Manchester**  
Cada bit é representado por uma *transição* de frequência no meio do período:
- `'1'` → 880 Hz (1ª metade) → 440 Hz (2ª metade)  
- `'0'` → 440 Hz (1ª metade) → 880 Hz (2ª metade)

A transição obrigatória a cada bit garante sincronização contínua.

**NRZI — Non-Return-to-Zero Inverted** *(a ser implementado)*  
A frequência é **invertida** quando o bit transmitido é `'1'` e **mantida** quando o bit é `'0'`. Isso elimina ambiguidade em sequências longas de `'1'`s e é  utilizado em USB e HDLC.

### Metricas

Metricas utilizadas 

#### Relação Sinal-Ruído (SNR)

$$\text{SNR}_{\text{dB}} = 10 \cdot \log_{10}\!\left(\frac{P_{\text{sinal}}}{P_{\text{ruído}}}\right)$$

SNR alto → canal de boa qualidade. SNR baixo ou negativo → canal degradado, com alta probabilidade de erro.

#### Taxa de Erro de Bit (BER)

$$\text{BER} = \frac{\text{bits incorretos}}{\text{total de bits transmitidos}}$$

BER = 0 indica transmissão perfeita. BER = 0,5 equivale a um resultado aleatório — a modulação falhou completamente.


## Estrutura das Entregas

Para este laborátorio vocês tem a disposição um notebook com 

Neste laboratório, você utilizaram modulação e demodulação de dados através da manipulação de frequências sonoras. 

Para tal vocês tem a disposição um notebook ([lab_notebook.ipynb 🐍](./lab_notebook.ipynb)) com um código base. Leia atentemente as instruções e utilize-o para praticar.

**Requisistos** : 

- Computador com Windows, Linux ou macOS (não testado)
- Python 3.x com bibliotecas:
  - `numpy`
  - `scipy` 
  - `matplotlib`  
- Interface de áudio (alto‐falante ou fone + microfone)  
  - Não funciona no Google Coolab
- Dispositivo externo (celular/tablet) para reprodução  

1. **Instale as bibliotecas necessárias:**

   ```bash
   pip install numpy matplotlib soundfile sounddevice scipy
   ```

2. **Teste o sistema de áudio** como as instruções do Setup do notebook.


### Entrega 1 — Relatório de Ruído *(pré-lab)*

> 📅 Deve ser concluída e submetida **antes** do dia do laboratório.

#### Tarefa 1.1 — Implementação do NRZI

Implemente as funções `encode_nrzi` e `decode_nrzi` no notebook. Os esqueletos com docstrings detalhadas estão fornecidos. Valide a implementação com a célula de teste incluída — todos os casos devem retornar `True`.

#### Tarefa 1.2 — Análise de Robustez ao Ruído

Utilizando a infraestrutura fornecida no notebook, você irá conduzir **dois conjuntos de experimentos** e comparar os resultados.

**Experimento A — Configuração padrão**

Execute o experimento com os parâmetros globais definidos no notebook (`BIT_DURATION = 0.5`, `FREQ_LOW = 440`, `FREQ_HIGH = 880`) e produza os gráficos e tabelas solicitados abaixo para as três modulações (NRZ, Manchester e NRZI).

**Experimento B — Variação de parâmetros**

Repita o experimento alterando ao menos **dois** dos parâmetros abaixo e documente os valores escolhidos:

| Parâmetro | Descrição | Sugestões de variação |
|:----------|:----------|:----------------------|
| `BIT_DURATION` | Duração de cada bit em segundos | 0.1 s, 0.25 s, 1.0 s, 2.0 s |
| `FREQ_LOW` / `FREQ_HIGH` | Frequências que representam '0' e '1' | Aproximar (ex: 440/550 Hz) ou afastar (ex: 300/1200 Hz) |
| `SAMPLE_RATE` | Taxa de amostragem | 22050 Hz, 8000 Hz |

Produza os mesmos gráficos e tabelas do Experimento A para cada configuração testada no Experimento B.


**Q1.1 — Curva SNR × BER**

Para cada experimento, gere um gráfico com o SNR no eixo x e a BER no eixo y. As três modulações (NRZ, Manchester e NRZI) devem aparecer no mesmo gráfico, cada uma com uma cor distinta e identificada na legenda. 

O objetivo é visualizar em que momento cada modulação começa a degradar e com que velocidade a taxa de erro cresce à medida que o canal piora. Abaixo do gráfico, inclua uma tabela resumindo os valores de SNR nos dois pontos críticos descritos em Q1.2.

> Nem sempre vocês verão o erro inicialmene, aumentem o tamanho da menssagem para aumentarem a chance de erro.

**Q1.2 — Pontos de degradação**

Com base no gráfico gerado em Q1.1, identifique para cada modulação os dois limiares de desempenho abaixo. O objetivo é entender a faixa operacional de cada esquema de codificação — isto é, em que condições de canal cada modulação ainda é utilizável e a partir de quando a comunicação se torna inviável.

| Modulação | SNR de primeiro erro — BER > 0 (dB) | SNR de falha total — BER ≥ 0,5 (dB) |
|:---------:|:-----------------------------------:|:------------------------------------:|
| NRZ | | |
| Manchester | | |
| NRZI | | |

O **SNR de primeiro erro** marca o ponto em que o canal começa a introduzir erros — a comunicação ainda é possível, mas já não é mais perfeita. O **SNR de falha total** marca o ponto em que a decodificação passa a ser equivalente a um chute aleatório, tornando a comunicação completamente inviável. A diferença entre esses dois valores indica a **margem de degradação** de cada modulação: uma margem larga significa que a modulação se deteriora gradualmente; uma margem estreita indica colapso abrupto.

Preencha a tabela para o Experimento A e repita para cada configuração testada no Experimento B.

**Q1.3 — Análise comparativa** *(mínimo 15 linhas)*

Com base nos gráficos e tabelas de Q1.1 e Q1.2, redija uma análise comparando o comportamento das três modulações. Discuta: qual modulação apresentou maior robustez ao ruído e por quê? Como a variação de `BIT_DURATION` afetou os resultados — taxas mais lentas tornam o sistema mais robusto? O afastamento ou aproximação das frequências (`FREQ_LOW`/`FREQ_HIGH`) impacta a capacidade de decodificação? Os resultados obtidos confirmam o comportamento esperado pela teoria vista em sala? Relacione ao menos um dos resultados com uma situação real de transmissão (modem, rádio, USB etc.).

> ⚠️ **A escrita do relatório deve ser de autoria própria do grupo. O uso de LLMs ou ferramentas de geração de texto para redigir as análises é vedado e poderá resultar em anulação da entrega.**

**Formato de entrega:** PDF do notebook exportado (Arquivo → Salvar e Exportar como PDF) + link para o repositório do grupo no Google Classroom.

> ⚠️ **Lembre-se que a organização é importante, desse modo verifiquem se eu conseguirei ler e entender o que foi escrito**


### Entrega 2 — Sistema de Chat por Áudio *(dia do lab)*

> 📅 O sistema deve estar funcional no dia do laboratório.

**Grupos de até 3 integrantes (os mesmos da Entrega 1).**
**Não há necessidade de interface, vocês podem utilizar um notebook**

O sistema a ser construído é um chat de camada física — isto é, a unidade de informação transmitida e recebida é o bit: `0` ou `1`. Não há codificação de caracteres, framing, detecção de erros ou qualquer protocolo de camadas superiores. A responsabilidade do sistema termina na entrega dos bits ao receptor, exatamente como ocorre na camada 1 do modelo OSI.

Isso significa que o grupo transmissor envia uma sequência de bits e o grupo receptor exibe a sequência de bits recebida — a interpretação do conteúdo é humana, não do sistema. A seguir temos um exemplo de menssagem transmitida:

```
00101010101111010101
```

Ou seja, voçês receberam um *string* com 0 e 1 transmitirão e decodificarão as menssagens durante a apresentação.

#### O que construir

Um sistema de terminal/notebook que permita:

1. **Transmitir** uma mensagem de texto digitada, convertendo-a em sinal de áudio via FSK  
    - Transmitir sinal de áudio - via alto falante.
    - Salvar arquivos .wav com sinais a serem transmitidos
2. **Receber** um sinal de áudio capturado pelo microfone e exibir a mensagem decodificada  
3. Selecionar a modulação em tempo de execução: **NRZ**, **NRZI** ou **Manchester**
4. Selecionar parametros basicos (via variavel de ambiente, ou arquivo de configuração)
    - `BIT_DURATION`
    - `FREQ_LOW` / `FREQ_HIGH`
    - `SAMPLE_RATE`

Não é exigida interface gráfica. Saídas via `print` são suficientes.

> ⚠️ Atenção: os parâmetros do canal serão variados pelo professor ao longo do laboratório. 

#### Critérios de Avaliação

A avaliação é realizada ao vivo: o professor fornece uma mensagem secreta e seleciona a modulação no momento da apresentação. O grupo deve transmiti-la e receber menssagens com sucesso, bem como responder a questionamentos.


## Orientações de Entrega

| Entrega | O que entregar | Onde | Prazo |
|:--------|:---------------|:-----|:------|
| **Entrega 1** | PDF do notebook + link do repositório | Google Classroom | Antes do lab |
| **Entrega 2** | Sistema funcional apresentado ao vivo | Laboratório | Dia do lab |


A entrega 1 poderá ser entregue como um **Jupyter Notebook** (`.ipynb`). Cada atividade combina células de código Python com células de texto em **Markdown**, onde você vai escrever suas respostas, análises e justificativas.

**Siga as instruções abaixo rigorosamente:**

1. **Células de código:** implemente o que é pedido, execute a célula e certifique-se de que a saída (gráficos, tabelas, valores) está visível no notebook antes de entregar.

2. **Células de resposta em Markdown:** para cada questão de reflexão ou análise, insira uma nova célula do tipo *Markdown* logo abaixo da célula de código correspondente.  
   Para criar uma célula Markdown no Jupyter, clique no menu suspenso que aparece como *“Code”* na barra de ferramentas e selecione *“Markdown”*.  
   Escreva sua resposta em texto corrido, com argumentação clara. Não é suficiente apenas mostrar números — explique o raciocínio.

3. **Equações no Markdown:** quando precisar escrever fórmulas matemáticas nas suas respostas, use notação LaTeX.  
   Use:

   - `$...$` para fórmulas *inline*  
   - `$$...$$` para fórmulas em bloco

   O Jupyter renderiza essas fórmulas automaticamente quando a célula é Markdown.

   Caso precise de referência, consulte o [markdownguide.org](https://www.markdownguide.org/extended-syntax/#latex)

   **Exemplo inline:**  
   Escreva:  
   ```
   A taxa de falsos positivos é $p = 0{,}01$
   ```

   **Exemplo em bloco:**
   ```md
   $$
   k = \frac{m}{n} \ln 2
   $$
   ```

4. **Exportação para PDF:** ao finalizar, exporte o notebook pelo menu:

   ```
   File → Save and Export Notebook As → PDF
   ```

   Abra o arquivo gerado e **verifique se todas as células, gráficos e equações estão legíveis** antes de submeter.

   Caso o seu ambiente não suporte exportação direta, utilize:

   ```
   File → Print → Salvar como PDF
   ```

#### ⚠️ Atenção

Entregas com células não executadas, gráficos ausentes, equações não renderizadas ou PDF ilegível **não serão aceitas**. Execute o notebook antes de exportar, com:

```
Kernel → Restart & Run All
```
