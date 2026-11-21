# Trabalho 5 (GRR): divisão do trabalho e guia de entrega

**Prazo:** segunda-feira, 23/11/2026, às 10h. **Entregáveis (Seção 6 do enunciado):** notebook, relatório PDF e apresentação de 10 min + 5 min de arguição.


## 2. O que já está pronto

```
randomization_dataprivacy/
├── trabalho5_grr.ipynb          ← notebook ÚNICO com todo o código (já executado, com saídas)
├── resultados/
│   ├── figuras/                 ← fig01, 02, 07, 08, 09 e 10 (PNG e PDF); fig03, 04 e 06 serão geradas pela Parte B
│   ├── tabelas/                 ← tab01–05 e tab09–15 (CSV, Markdown e LaTeX); tab06, 07 e 08 serão geradas pela Parte B
│   ├── resultados_brutos.csv    ← erro de cada consulta em cada execução (4.000 linhas)
│   └── resultados_brutos_eps_estendido.csv
├── LEIAME.md                    ← este guia
├── Trab5_Resposta_Randomica.pdf ← enunciado
└── dados_censo.csv              ← dados
```

**Notebook** (`trabalho5_grr.ipynb`): os "módulos separados" pedidos no enunciado são as seções 2 a 5.

| Seção | Conteúdo | Parte |
|---|---|---|
| 0–1 | Configuração (ε, R = 10, sementes) e dados | A |
| 2 | **Módulo GRR**: cliente (`grr_perturbar_usuario`, `grr_perturbar_vetor`) e servidor (`grr_estimar_frequencias`) | A |
| 3 | **Módulo Consultas**: Q1–Q10 como retângulos Idade × Salário | A |
| 4 | **Módulo Estratégias**: E1 e E2 (bins); `cliente_*` × `servidor_*` | A |
| 5 | **Módulo Avaliação**: MAE, execução do protocolo, erro teórico (viés + variância) | A |
| 6 | 7 testes automáticos de corretude (todos passam) | A |
| 7 | Escolha e justificativa do tamanho do bin, feita a partir do workload | A |
| 8 | Experimentos + **tabela-resumo da configuração experimental** (pronta para o relatório) | A |
| **9.1–9.3** | **Tabelas e figuras obrigatórias + célula de conferência** | **B** |
| 9.4–9.8 | Alinhamento, viés × ruído, truncamento, validação, ε estendido | A |
| 10 | **Respostas às 4 perguntas de análise + conclusões**, com os números | A |

---

## 3. Resultados principais (para o relatório e os slides)

**MAE médio ± desvio-padrão (R = 10), sem truncamento:**

| ε | E1 (domínio conjunto, k = 148) | E2 (bins de 10 anos, k = 16) | E1 ÷ E2 |
|---|---|---|---|
| 0,5 | 13.163 ± 4.402 | 1.685 ± 624 | 7,8× |
| 1 | 4.866 ± 1.830 | 671 ± 216 | 7,2× |
| 2 | 1.345 ± 438 | 191 ± 54 | 7,1× |
| 4 | 192 ± 38 | 79 ± 15 | 2,4× |

**Cinco mensagens do trabalho:**

1. **E2 vence E1 em todos os ε do protocolo.** O ruído do GRR cresce com o tamanho do domínio k e com o número de células que a consulta soma, e o E2 reduz os dois (k de 148 para 16; no máximo 8 células por consulta, contra até 82).
2. **Bins trocam variância por viés.** Quando o limite de uma consulta atravessa um bin, o servidor supõe idades uniformes dentro do bin e erra de forma sistemática (até 122 pessoas). Esse viés **não diminui com ε**. Por isso a vantagem do E2 cai de 7,8× para 2,4×, e o E1 passaria a vencer para ε ≳ 6,1 (Fig. 10).
3. **Bin escolhido: w = 10 alinhado às dezenas** ([17–19], [20–29], …, [80–90]). Todos os limites das consultas estão nas dezenas, então 5 consultas ficam exatas e as outras 5 atravessam um bin em uma única idade. É a melhor largura em ε = 2, empata em ε = 4 e nunca fica mais que 1,54× pior que a melhor largura de cada ε. Com ε ≤ 1, bins de 15 anos são um pouco melhores, porque o ruído domina.
4. **O alinhamento importa tanto quanto a largura.** A mesma largura 10 começando em 17 ([17–26], [27–36], …) faz 9 das 10 consultas atravessarem bins e fica 3,8× pior em ε = 4.
5. **Truncar a resposta em [0, n] só ajuda** (−28% no E1 com ε = 0,5). Truncar cada célula atrapalha muito (3,7× pior no E1).

---

## 4. Parte B: código (Seções 9.1–9.3 do notebook)

Gerar as 3 tabelas e as 3 figuras que o item 2 da Seção 6.2 do enunciado exige. A especificação completa está no notebook: no começo da Seção 9 (o que está disponível, com exemplos de acesso) e em cada subseção (o que gerar). Resumo:

| Seção | Tabela: CSV em formato longo, com estas colunas | Figura | Exigência do enunciado |
|---|---|---|---|
| 9.1 | `tab06_mae_E1_E2`: `estrategia`, `epsilon`, `mae`, `dp`, `mae_trunc`, `dp_trunc` (8 linhas) | `fig03_mae_vs_eps` | MAE × ε para E1 e E2, **indicando a escala log** |
| 9.2 | `tab07_mae_por_consulta_eps1`: `estrategia`, `epsilon`, `consulta`, `mae`, `dp` (20 linhas) | `fig04_mae_por_consulta_eps1` | MAE de cada consulta para um ε fixo (ε = 1) |
| 9.3 | `tab08_larguras_bin`: `estrategia`, `epsilon`, `mae`, `dp` (24 linhas) | `fig06_larguras_bin` | comparação do MAE entre as larguras de bin |

**Passo a passo:**
1. Abrir o notebook e rodar tudo uma vez (*Run All*, cerca de 30 s). Assim `resumo` e `pq` ficam carregados.
2. Escrever o código nas três células marcadas `# PARTE B · 9.x — implementar aqui`, usando os utilitários prontos: `salvar_tabela`, `salvar_figura`, `fmt`, `fmt_media_dp`, `eixo_log` e a paleta (`COR_E1`, `COR_E2`, `RAMPA_EPS`). Exemplos de acesso: `resumo.loc[("E1", 0.5), "mae"]`, `resumo.loc[NOME_E2]`, `pq.loc[("E1", 1.0)].reindex(IDS_CONSULTAS)`.
3. Rodar a célula **Conferência da Parte B**, no fim da Seção 9.3. Cada item passa de "pendente" para ✓ quando o arquivo existe e os números batem com o protocolo. Um ✗ explica o que está errado (coluna faltando, número formatado como texto etc.).
4. No final, rodar tudo de novo para que o notebook entregue fique com todas as saídas.

Estimativa: 2 a 3 horas para quem já usou pandas e matplotlib. O visual das figuras é livre, desde que atenda à especificação.

---

## 5. Parte B: relatório PDF

O enunciado pede um relatório **conciso**. Sugestão: 5 a 6 páginas, nesta estrutura. Os textos-base já estão no notebook, então o trabalho é condensar e redigir.

| Seção do relatório | O que escrever | Usar | Fonte no notebook |
|---|---|---|---|
| **1. Introdução** | Cenário (agregador sem acesso aos dados), LDP, GRR: fórmulas de p e q, p/q = e^ε, estimador não viesado | `tab02` (p cai com k) | Seções 2 e 4 |
| **2. Configuração experimental** *(item 1 do enunciado)* | Dataset, workload, ε, R = 10, **sementes 2026–2035 (iguais em todas as configurações)**, pós-processamento (truncamento em [0, n], reportado com e sem), regra para bins atravessados (fração uniforme) | `tab01`, `fig01`, `tab03` | **Tabela-resumo da Seção 8** |
| **3. Escolha do bin** | Cortes exigidos pelo workload; por que w = 10 alinhado; candidatas avaliadas | `fig02`, `tab04`, `tab05` | Seção 7 |
| **4. Resultados** *(item 2)* | **Obrigatórios:** (a) MAE × ε para E1 e E2, **dizendo que o eixo vertical está em escala log**; (b) MAE por consulta para um ε fixo; (c) comparação entre larguras de bin. Mais: alinhamento e truncamento | (a) `fig03` + `tab06`; (b) `fig04` + `tab07`; (c) `fig06` + `tab08` (**geradas pela Parte B**); `fig07`; `tab10` | Seção 9 |
| **5. Análise** *(item 3)* | As **4 perguntas**, uma por parágrafo | `fig08`, `fig10`, `tab13`, `tab14` | **Seção 10, "Respostas"** |
| **6. Conclusão** | 4–5 frases | — | Seção 10, "Conclusões" |
| *Apêndice (opcional)* | Validação da implementação: testes e teoria × empírico | `fig09`, `tab11` | Seções 6 e 9.7 |

Dicas:
- **Figuras:** use o PDF em LaTeX e o PNG em Word/Docs. As tabelas `.tex` usam `booktabs` (`\usepackage{booktabs}`).
- **Legendas obrigatórias:** em toda figura com eixo log, escreva "eixo vertical em escala logarítmica" e "média de R = 10; barras = ±1 desvio-padrão".
- **Referências:** Wang et al. (USENIX Security 2017), Dwork & Roth (2014) e Warner (1965), as sugeridas no enunciado.

---

## 6. Parte B: slides (10 min)

Roteiro sugerido, com 11 slides. Cada um apresenta a parte que fez, inclusive as figuras do próprio código, mas os dois devem conseguir apresentar qualquer slide.

| # | Slide | Tempo | Quem | Material |
|---|---|---|---|---|
| 1 | Título e equipe | 0:15 | B | — |
| 2 | O problema: agregador × privacidade local | 0:45 | B | — |
| 3 | GRR: p, q, garantia ε-LDP, estimador; "com ε = 1, só 1,8% dos usuários do E1 dizem a verdade" | 1:15 | A | `tab02` |
| 4 | Dados e workload | 0:45 | B | `fig01`, `tab03` |
| 5 | E1 × E2 e a escolha do bin (cortes nas dezenas) | 1:30 | A | `fig02` |
| 6 | Implementação: cliente × servidor, testes, sementes | 0:45 | A | trecho de código |
| 7 | **Resultado principal: MAE × ε** | 1:00 | B | `fig03` |
| 8 | MAE por consulta: quais são as mais e as menos precisas | 0:45 | B | `fig04` |
| 9 | Largura e alinhamento dos bins | 1:00 | B | `fig06`, `fig07` |
| 10 | Viés × ruído, e por que a vantagem cai com ε | 1:15 | A | `fig08`, `fig10` |
| 11 | Conclusões | 0:45 | B | — |
| | **Total** | **10:00** | | |

---

## 7. Preparação para a arguição (os dois)

<details open>
<summary><b>Perguntas prováveis e respostas curtas</b></summary>

1. **Por que o GRR satisfaz ε-LDP?** Para quaisquer entradas v, v′ e saída y, a razão Pr[y | v] / Pr[y | v′] vale 1, p/q ou q/p, e p/q = e^ε.
2. **Por que o estimador é não viesado?** E[c_y] = n_y·p + (n − n_y)·q, logo E[(c_y − nq)/(p − q)] = n_y.
3. **Por que o E2 erra menos?** Var ≈ n·|S|·(e^ε + k − 1 − |S|)/(e^ε − 1)². O ruído cresce com k e com o número de células |S|, e o E2 reduz os dois. Com ε = 1, p sobe de 1,8% para 15,3%.
4. **Por que w = 10 e alinhado às dezenas?** Os limites das consultas estão em 20, 30, 40, 50 e 60. Com a grade alinhada, 5 consultas ficam exatas e 5 atravessam um bin em só 1 idade (1/10). Tem o menor pior caso entre as larguras (1,54× do ótimo).
5. **Por que não escolher o bin olhando os dados?** Seria usar os dados privados sem orçamento de privacidade. A escolha usa só o workload, que é público; os experimentos apenas confirmam.
6. **O que acontece quando a consulta atravessa um bin?** O servidor soma a fração do bin proporcional às idades cobertas (hipótese de uniformidade). Como a distribuição real não é uniforme, aparece um viés, por exemplo +99 em Q4 e −122 em Q1, que não diminui com ε.
7. **Por que a vantagem do E2 cai com ε?** O ruído cai com ε e o viés fica constante. Em ε = 4 o viés já domina as consultas que atravessam bins, e para ε ≳ 6,1 o E1 vence.
8. **Com ε pequeno, w = 15 é melhor. Por que não escolheram 15?** Com ε ≤ 1 o ruído domina, e bins maiores (menos células) ganham por pouco (≈ 9% em ε = 1). Mas w = 15 fica 2× pior em ε = 2 e 5× pior em ε = 4. w = 10 é a escolha robusta para a faixa toda.
9. **O truncamento afeta a privacidade?** Não: é pós-processamento da saída do mecanismo. Truncamos a resposta da consulta e não cada célula, porque zerar células negativas cria viés positivo que se acumula (3,7× pior no E1).
10. **Onde fica a separação cliente/servidor no código?** As funções `cliente_*` e `grr_perturbar_*` veem a idade e o salário. As funções `servidor_*` recebem só os reports, o ε e a partição pública. Os dados originais aparecem apenas para gerar os reports e para calcular as respostas verdadeiras.
11. **A versão vetorizada respeita "cada usuário independentemente"?** Sim. Cada usuário tem os seus próprios sorteios, e o teste T2 mostra que a distribuição é a mesma da função de 1 usuário.
12. **Por que as mesmas sementes em todas as configurações?** Reprodutibilidade e comparações pareadas. As 10 repetições de uma mesma configuração usam sementes diferentes, então são independentes.
13. **O desvio-padrão é grande (≈ 30% da média). Os resultados são confiáveis?** Com R = 10 a média tem cerca de 10% de erro-padrão. Por isso validamos com R = 1.000 contra a fórmula teórica: razão entre 0,98 e 1,01 (`tab11`).
14. **O que é o "MAE teórico"?** Viés e variância exatos do estimador, que é linear nas contagens. Pelo TCL a resposta é praticamente normal, e o erro esperado é a média da normal dobrada. Usa os dados reais, então serve **só para análise**.
15. **Por que Q3 tem erro relativo enorme?** O ruído do LDP é aditivo e não depende do tamanho da resposta. Q3 tem só 985 pessoas (2% de n).
16. **E2\* (bins do workload): por que não é a escolha principal?** Não tem largura uniforme (o enunciado pede um tamanho de bin) e soma mais células por consulta, então perde em ε ≤ 2. Sem viés, é a melhor em ε = 4.
17. **Por que há um pico na idade 90 do histograma?** Provavelmente *top-coding* do censo (idades ≥ 90 gravadas como 90). Não afeta nada, porque 90 está no bin [80–90].
18. **Por que os gráficos de MAE usam escala logarítmica?** O MAE por consulta varia quase três ordens de grandeza (de cerca de 30 a mais de 20.000) entre estratégias, consultas e valores de ε. Em escala linear, as curvas do E2 e de ε alto ficariam coladas no zero.
19. **O que as barras de erro mostram?** ± 1 desvio-padrão do MAE nas 10 repetições, ou seja, a variação de uma execução para outra. Não é intervalo de confiança da média, que seria cerca de √10 ≈ 3× mais estreito.

</details>

**Decisões de implementação a defender:** implementação própria do GRR (sem bibliotecas de LDP); desvio-padrão amostral (ddof = 1); truncamento por consulta; bins atravessados por fração uniforme; o primeiro bin [17–19] é mais curto porque o domínio começa em 17; a idade 90 é incorporada ao último bin ("ajustando o último intervalo").

---

## 8. Checklist até a entrega

- [ ] **Parte B, código:** implementar as Seções 9.1–9.3 até a célula de conferência mostrar ✓ nos 6 itens
- [ ] Rodar o notebook inteiro (*Run All*) para que a versão entregue tenha todas as saídas
- [ ] Preencher os nomes da equipe na 1ª célula do notebook e no relatório
- [ ] Escrever o relatório PDF (Seção 5 deste guia)
- [ ] Montar os slides (Seção 6)
- [ ] Ensaiar com cronômetro (≤ 10 min)
- [ ] Os dois lerem a Seção 7 e as Seções 7 e 10 do notebook
- [ ] **Confirmar com o professor** se "módulos separados" (Seção 6.1 do enunciado) aceita seções dentro de um único notebook, que foi a opção da equipe. Se não aceitar, as seções 2–5 viram arquivos `.py` sem mudar a lógica.
- [ ] Submeter notebook + PDF (+ slides, se pedido) até 23/11, 10h
