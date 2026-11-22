# Privacidade Diferencial Local com Mecanismo de Resposta Randômica Generalizada (GRR)

Implementação e análise experimental do mecanismo **GRR (Generalized Randomized Response)** sob **Privacidade Diferencial Local ($\epsilon$-LDP)**, avaliando o compromisso entre viés de discretização e variância estatística sobre a base `dados_censo.csv` ($n = 48.842$).

## Equipe
* Monique Damasceno Gomes
* Paulo Guilherme de Almeida Silva

## Visão Geral dos Experimentos
O estudo compara duas estratégias de codificação conjunta dos atributos `Idade` ($[17, 90]$) e `Sal` ($\le 50\text{K}$ ou $> 50\text{K}$) sob um *workload* de 10 consultas de contagem ($Q_1$ a $Q_{10}$) e quatro orçamentos de privacidade ($\epsilon \in \{0{,}5;\; 1{,}0;\; 2{,}0;\; 4{,}0\}$), com $R = 10$ repetições por configuração:

* **Estratégia E1 (Domínio conjunto exato):** preserva todas as 74 idades individuais combinadas ao salário binário ($k = 148$ estados). Não possui viés de discretização, mas sofre alta variância.
* **Estratégia E2 (Discretização em bins):** agrupa a idade em intervalos de largura $w = 10$ alinhados às dezenas ($k = 16$ estados). Reduz o Erro Médio Absoluto (MAE) entre **2,4× e 7,8×** em relação a E1 na faixa avaliada.

## Estrutura do Repositório
* `trabalho5_grr.ipynb`: Notebook principal com a implementação do GRR, estimador não viesado, simulações experimentais e geração de gráficos e tabelas.
* `dados_censo.csv`: Conjunto de dados original utilizado nos experimentos.
* `Relatorio_Trabalho5_GRR.pdf`: Relatório técnico completo com fundamentação teórica, metodologia e discussão dos resultados.
* `resultados/`: Pasta gerada automaticamente contendo figuras (`figuras/`) e tabelas (`tabelas/`) em `.csv`, `.md`, `.tex` e `.pdf`.

## Requisitos e Execução
O notebook requer Python 3 e as seguintes bibliotecas:

```bash
pip install numpy pandas scipy matplotlib