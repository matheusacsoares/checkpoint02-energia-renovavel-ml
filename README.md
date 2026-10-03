# Checkpoint 2 – APIs, energias renováveis e Machine Learning

Trabalho do Checkpoint 2 da disciplina SERS (1CC). Usamos duas APIs públicas para montar dois conjuntos de dados e, em cada um, treinamos e comparamos três algoritmos de Machine Learning.

## Integrantes

- Lucas Bellezzo Figueiredo – RM569734
- Maria Luiza Vieira de Freitas – RM571535
- Giovanna Ferreira Almeida – RM571822
- Matheus Arruda Camara Soares – RM571594
- Matheus Sabino da Silva Guedes – RM572907

## Objetivo

- **Tarefa 1 (classificação):** prever a fonte de um empreendimento de geração (Solar, Eólica ou Hidráulica) a partir da potência outorgada, da latitude e da longitude.
- **Tarefa 2 (regressão):** estimar a radiação solar (W/m²) em Petrolina (PE) a partir de temperatura, umidade, nuvens, vento e hora do dia.

## Dados

| Arquivo | Fonte | Período / recorte | Linhas |
|---|---|---|---|
| `aneel_classificacao_orange.csv` | [SIGA – ANEEL](https://dadosabertos.aneel.gov.br/dataset/siga-sistema-de-informacoes-de-geracao-da-aneel) (API CKAN, sem token) | Cadastro atual; até 1200 registros por tipo (UFV, EOL, UHE, PCH, CGH) | 3876 |
| `meteo_regressao_orange.csv` | [Open-Meteo – Historical Weather API](https://open-meteo.com/en/docs/historical-weather-api) (sem token) | 01/04/2025 a 30/06/2025, horário local (`America/Recife`), das 7h às 17h, coordenadas −9,39 / −40,50 | 1001 |

Os dois CSVs deste repositório foram gerados pelo próprio notebook. Como o cadastro da ANEEL é atualizado com frequência, rodar a consulta de novo pode trazer números um pouco diferentes.

## Como executar

**No Google Colab:** abra o notebook `Aula_APIs_Energia_Renovavel_ML.ipynb` pelo botão "Open in Colab" e use *Ambiente de execução → Executar tudo*. As bibliotecas necessárias já vêm instaladas no Colab.

**Localmente:**

```bash
pip install pandas numpy matplotlib seaborn scikit-learn jupyter
jupyter notebook Aula_APIs_Energia_Renovavel_ML.ipynb
```

As células devem ser executadas em ordem. As primeiras consultam as APIs e salvam os dois CSVs na mesma pasta, e as células de ML leem esses arquivos. Se a API da ANEEL estiver fora do ar, a célula logo depois dos imports tem uma linha comentada que carrega o CSV direto do repositório do professor.

## Resultados

### Tarefa 1 – Classificação

Divisão 80/20 estratificada, `random_state=42`. Precision, Recall e F1 com média **macro**.

| Modelo | Accuracy | Precision | Recall | F1 |
|---|---|---|---|---|
| KNN (com padronização) | 0,966 | 0,968 | 0,965 | 0,966 |
| Regressão Logística (com padronização) | 0,823 | 0,828 | 0,820 | 0,818 |
| **Random Forest** | **0,976** | **0,977** | **0,974** | **0,975** |

### Tarefa 2 – Regressão

Divisão temporal: primeiras 80% das horas para treino (01/04 a 12/06) e últimas 20% para teste (12/06 a 30/06), sem embaralhar.

| Modelo | MAE (W/m²) | MSE ((W/m²)²) | R² |
|---|---|---|---|
| Regressão Linear | 145,2 | 30034 | 0,360 |
| Árvore de Decisão (`max_depth=5`) | 90,4 | 14479 | 0,691 |
| **Random Forest** | **66,8** | **7307** | **0,844** |

## Conclusões

**Tarefa 1:** o Random Forest teve o melhor resultado, com o KNN logo atrás. A Regressão Logística ficou bem abaixo porque separa as classes com fronteiras retas, e as fontes se distribuem em regiões do mapa que não se separam assim. A classe mais confundida foi a Solar. O resultado alto tem limitações: a amostra é limitada a 1200 registros por tipo e não representa a proporção real, as fontes aparecem concentradas em algumas regiões e muitas usinas solares têm potência cadastrada de 1 kW. Por isso não dá para concluir que potência e localização bastam para identificar a fonte de qualquer usina.

**Tarefa 2:** o Random Forest foi o melhor (R² de 0,84). A hora do dia é a variável mais importante, mas a relação dela com a radiação tem formato de sino (pico perto do meio-dia). Os modelos de árvore capturam isso e a Regressão Linear não. O que foi previsto é a radiação em superfície horizontal, estimada por modelo/reanálise. Isso não é o mesmo que a energia gerada por um sistema fotovoltaico, que também depende de área, eficiência, inclinação e temperatura dos painéis, perdas do inversor, sujeira e sombreamento.

## Estrutura do repositório

```text
├── README.md
├── Aula_APIs_Energia_Renovavel_ML.ipynb
├── aneel_classificacao_orange.csv
└── meteo_regressao_orange.csv
```
