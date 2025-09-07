# Relatório Executivo — Previsão de Hipertensão

Visão geral  
Projeto realizado como trabalho final do Curso de Pós‑Graduação em *Machine Learning & Analytics* pela PUC‑RIO.
Objetivo: avaliar a viabilidade de um modelo de classificação capaz de identificar pacientes com hipertensão a partir de variáveis clínicas e demográficas. 
Dados: 1.985 registros ([dados/hypertension_dataset.csv](dados/hypertension_dataset.csv)). 
Código e todas as saídas estão em [notebooks/hypertension_prediction.ipynb](notebooks/hypertension_prediction.ipynb).

Principais conclusões (alto nível)
- Modelo final selecionado: AdaBoost (estimador preservado em [modelos/AdaBoost_model.pkl](modelos/AdaBoost_model.pkl)).  
- Desempenho no conjunto de teste:
  - Acurácia: ~95.5%  
  - Recall (métrica priorizada): ~95.5%  
  - Reporte de classificação (resumo): Classe 0 — recall 1.00 (support 191); Classe 1 — recall 0.91 (support 206).  
- Otimização por validação cruzada (10 folds) encontrou melhor conjunto: {'model__learning_rate': 0.1, 'model__n_estimators': 450} com CV recall ≈ 0.944 (tempo de busca ≈ 85 s). Saídas detalhadas no notebook (GridSearchCV).
- Diferença em relação ao baseline (DummyClassifier) é estatisticamente significativa (teste de McNemar: estatística = 11.0; p‑valor ≪ 0.05).

Sequência das análises (fluxo do relatório)
1. Preparação do ambiente e importação de bibliotecas.  
2. Carga e verificação dos dados: formato (1985×11), verificação de nulos (Medication ≈ 40% nulos) e duplicatas (nenhuma).  
3. Análise exploratória: estatísticas descritivas e identificação visual de dispersões/outliers (boxplots).  
4. Pré‑processamento:
   - Separação target/features e divisão treino/teste (stratified).  
   - Transformações: RobustScaler para numéricas; OrdinalEncoder para binárias; OneHotEncoder + imputação ("missing_value") para categóricas; seleção de 8 features via SelectKBest. Pipeline em [`preprocess`](notebooks/hypertension_prediction.ipynb).  
5. Treinamento comparativo: vários modelos testados (Logistic, SVM, RandomForest, XGB, LGBM, CatBoost, AdaBoost, etc.). AdaBoost foi o melhor no conjunto de teste.  
6. Validação cruzada e tuning de hiperparâmetros: GridSearchCV com scoring por recall ponderado; resultados e melhores parâmetros publicados no notebook.  
7. Avaliação final: predição sobre X_test, relatório de classificação e matriz de confusão. Código de avaliação em [`evaluate_classification`](notebooks/hypertension_prediction.ipynb).  
8. Validação estatística: comparação com baseline via [`make_crontingency_table`](notebooks/hypertension_prediction.ipynb) + teste de McNemar.  
9. Treino final com todo o dataset e salvamento do modelo final ([modelos/AdaBoost_model.pkl](modelos/AdaBoost_model.pkl)).

Gráficos e saídas relevantes (para apresentação não‑técnica)
- Boxplots das variáveis numéricas (Salt_Intake, Sleep_Duration, BMI) — mostra distribuição e limites aceitáveis.  
- Gráfico de barras da distribuição do target (Has_Hypertension) — indica classes equilibradas.  
- Tabela comparativa de modelos (accuracy, f1, recall, precision, auc) — AdaBoost em destaque.  
- Matriz de confusão do modelo final — mostra como os acertos/erros se distribuem por classe.  
Todos os gráficos e tabelas estão no [notebooks/hypertension_prediction.ipynb](notebooks/hypertension_prediction.ipynb).

Números-chave (extraídos do notebook)
- Formato dos dados: 1985 registros × 11 colunas.  
- Percentual de nulos em Medication: 40.25%.  
- Formato após preprocess + seleção: treino (1588, 8).  
- Métricas comparativas (treino/teste): AdaBoost — accuracy ≈ 0.95466, recall ≈ 0.95466.  
- GridSearchCV: "Fitting 10 folds for each of 60 candidates" → Tempo ≈ 85.47 s, melhor CV recall ≈ 0.94394, melhores parâmetros = {'model__learning_rate': 0.1, 'model__n_estimators': 450}.  
- McNemar: estatística = 11.0, p‑valor muito menor que 0.05 (diferença significativa frente ao baseline).

Artefatos e pontos de inspeção técnica
- Notebook com todas as etapas e saídas: [notebooks/hypertension_prediction.ipynb](notebooks/hypertension_prediction.ipynb).  
- Dados brutos: [dados/hypertension_dataset.csv](dados/hypertension_dataset.csv).  
- Modelo final salvo: [modelos/AdaBoost_model.pkl](modelos/AdaBoost_model.pkl).  
- Funções/objetos úteis no notebook: [`evaluate_classification`](notebooks/hypertension_prediction.ipynb), [`make_crontingency_table`](notebooks/hypertension_prediction.ipynb), [`preprocess`](notebooks/hypertension_prediction.ipynb), estimador final em [`search.best_estimator_`](notebooks/hypertension_prediction.ipynb).

Recomendações (não técnicas)
- Validar o modelo em dados externos/real‑world antes de qualquer uso operacional.  
- Revisar o tratamento de valores ausentes em Medication com especialistas clínicos (imputação por categoria "missing_value" pode esconder padrões).  
- Preparar material para stakeholders: painel com 3 gráficos (distribuição do target, matriz de confusão, comparativo de modelos) e um resumo executivo sobre implicações de falsos negativos/positivos.  
- Documentar decisões (por que 8 features, por que encoding/imputação) para rastreabilidade clínica e auditoria.

Onde encontrar detalhes de implementação e saídas
- Notebook completo: [notebooks/hypertension_prediction.ipynb](notebooks/hypertension_prediction.ipynb) — contém código, gráficos e logs (ex.: tempo de GridSearch, relatório de classificação, print de p‑valor do McNemar).  
- Funções: [`evaluate_classification`](notebooks/hypertension_prediction.ipynb), [`make_crontingency_table`](notebooks/hypertension_prediction.ipynb).  
- Pipeline de pré‑processamento: [`preprocess`](notebooks/hypertension_prediction.ipynb).  
- Resultado do tuning: ver célula GridSearchCV em [notebooks/hypertension_prediction.ipynb](notebooks/hypertension_prediction.ipynb).

Contato/Próximo passo sugerido
- Preparar apresentação visual e um documento de governança com decisões de pré‑processamento e validação clínica; em seguida, executar validação externa com dados reais ou amostra hold‑out