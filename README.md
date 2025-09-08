# Relatório Executivo — Previsão de Hipertensão

Resumo executivo  
Este projeto avaliou a viabilidade de um modelo de Machine Learning para identificar pacientes com hipertensão a partir de variáveis clínicas e demográficas. O estudo utilizou um conjunto de dados sintético, porém realista, com 1.985 registros. O fluxo do trabalho seguiu uma sequência lógica: carga dos dados → exploração → pré‑processamento → comparação de modelos → validação cruzada e otimização → avaliação final → preservação do modelo.

Onde ver o código e artefatos
- Notebook com todo o passo a passo, gráficos e saídas: [notebooks/hypertension_prediction.ipynb](notebooks/hypertension_prediction.ipynb)  
- Dados brutos utilizados: [dados/hypertension_dataset.csv](dados/hypertension_dataset.csv)  
- Modelo final preservado: [modelos/AdaBoost_model.pkl](modelos/AdaBoost_model.pkl)  
- Funções e objetos relevantes (no notebook): [`evaluate_classification`](notebooks/hypertension_prediction.ipynb), [`make_crontingency_table`](notebooks/hypertension_prediction.ipynb), [`preprocess`](notebooks/hypertension_prediction.ipynb), estimador final em [`search.best_estimator_`](notebooks/hypertension_prediction.ipynb)

Objetivo do estudo
- Verificar se um classificador pode prever a presença de hipertensão com desempenho significativamente superior a um classificador de referência (baseline).
- Priorizar a métrica recall (capturar casos positivos reais) devido ao custo associado a falsos negativos no contexto clínico.

Resumo dos resultados (alto nível)
- Modelo selecionado: AdaBoost (modelo final salvo em [modelos/AdaBoost_model.pkl](modelos/AdaBoost_model.pkl)).  
- Desempenho no conjunto de teste (exemplos extraídos do notebook):
  - Acurácia ≈ 95.5%  
  - Recall ≈ 95.5% (métrica priorizada)  
  - Relatório de classificação: Classe 0 — recall 1.00 (support 191); Classe 1 — recall 0.91 (support 206).  
- Validação cruzada e tuning (GridSearchCV, scoring = recall ponderado, 10 folds):
  - Melhor score CV ≈ 0.94394  
  - Melhores hiperparâmetros: {'model__learning_rate': 0.1, 'model__n_estimators': 450}  
  - Busca com múltiplas combinações (tempo total da busca informado no notebook).
- Validação estatística:
  - Comparação com o baseline (DummyClassifier) usando teste de McNemar.
  - Estatística de teste = 11.0; p‑valor ≪ 0.05 → diferença estatisticamente significativa (ver célula do teste em [notebooks/hypertension_prediction.ipynb](notebooks/hypertension_prediction.ipynb)).

Fluxo de trabalho (na ordem executada)
1. Preparação do ambiente e definição de semente (reprodutibilidade).  
2. Carga e inspeção inicial dos dados — formato: 1985 linhas × 11 colunas; coluna Medication com ~40.25% de valores nulos.  
3. Análise exploratória (EDA): estatísticas descritivas, boxplots e verificação de duplicatas (nenhuma encontrada).  
4. Pré‑processamento:
   - Separação target/features (target = Has_Hypertension) e divisão treino/teste estratificada.  
   - Transformações: RobustScaler para numéricas; OrdinalEncoder para binárias; SimpleImputer("missing_value") + OneHotEncoder (drop="first") para categóricas múltiplas; seleção de 8 features via SelectKBest. Pipeline em [`preprocess`](notebooks/hypertension_prediction.ipynb).  
5. Treinamento comparativo: pipelines com vários modelos (Logistic, SVM, RandomForest, XGB, LGBM, CatBoost, AdaBoost, entre outros). AdaBoost destacou‑se nas métricas.  
6. Validação cruzada e otimização de hiperparâmetros do AdaBoost (GridSearchCV com scoring por recall ponderado).  
7. Avaliação final no conjunto de teste: relatório de classificação e matriz de confusão gerada no notebook.  
8. Validação estatística com teste de McNemar usando a função [`make_crontingency_table`](notebooks/hypertension_prediction.ipynb).  
9. Treinamento final com todo o dataset e salvamento do modelo em [modelos/AdaBoost_model.pkl](modelos/AdaBoost_model.pkl).

Principais gráficos gerados (úteis para apresentação não técnica)
- Boxplots das variáveis numéricas (Salt_Intake, Sleep_Duration, BMI) — mostram distribuição e amplitude.  
- Gráfico de barras da distribuição do target — confirma equilíbrio entre classes.  
- Tabela comparativa de modelos (accuracy, f1, recall, precision, auc) — AdaBoost em destaque.  
- Matriz de confusão do modelo final — apresenta visualmente acertos e erros por classe.  
Todos os gráficos estão no notebook: [notebooks/hypertension_prediction.ipynb](notebooks/hypertension_prediction.ipynb).

Números‑chave (extraídos do notebook)
- Registros: 1.985 | Atributos: 11.  
- Percentual de nulos em Medication: 40.25%.  
- Dimensão após preprocess + SelectKBest: treino (1588, 8).  
- Métricas do melhor modelo (teste): accuracy ≈ 0.95466; recall ≈ 0.95466.  
- GridSearchCV: melhor CV recall ≈ 0.94394; best_params = {'model__learning_rate': 0.1, 'model__n_estimators': 450}.  
- McNemar: estatística = 11.0, p‑valor extremamente baixo (≪ 0.05).

Limitações e observações
- Dados sintéticos: necessários testes adicionais com dados reais antes de uso operacional clínico.  
- Imputação de Medication como "missing_value": decisão prática — recomendar revisão com especialistas clínicos para entender impacto.  
- Embora métricas sejam altas, avaliar custo operacional de falsos negativos e falsos positivos dependendo do uso final.

Recomendações práticas (próximos passos)
- Validar desempenho em um conjunto externo real (validação externa).  
- Construir material de apresentação para stakeholders com 3 gráficos essenciais: distribuição do target, matriz de confusão e comparativo de modelos.  

Referências de implementação (no notebook)
- Função de avaliação: [`evaluate_classification`](notebooks/hypertension_prediction.ipynb)  
- Criação da tabela de contingência: [`make_crontingency_table`](notebooks/hypertension_prediction.ipynb)  
- Pipeline de pré‑processamento: [`preprocess`](notebooks/hypertension_prediction.ipynb)  
- Estimador obtido pela busca: [`search.best_estimator_`](notebooks/hypertension_prediction.ipynb)

Contato técnico / inspeção rápida
- Para reproduzir: abrir [notebooks/hypertension_prediction.ipynb](notebooks/hypertension_prediction.ipynb) e executar as células na sequência.  
- Para inspecionar o dataset: [dados/hypertension_dataset.csv](dados/hypertension_dataset.csv).  
- Para carregar o modelo final em Python: joblib.load("modelos/AdaBoost_model.pkl").

Conclusão
O estudo indica que um modelo de classificação (AdaBoost) consegue prever casos de hipertensão neste conjunto de dados com alta acurácia e recall. Recomenda‑se validação externa e revisão clínica dos tratamentos de dados antes de qualquer uso operacional.