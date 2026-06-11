# Lista3

# Descrição da Abordagem Adotada

O projeto foi executado através de uma pipeline estruturada de Machine Learning Supervisionado para classificação binária. O algoritmo escolhido foi o K-Nearest Neighbors (KNN) com valor de K=5.

A estratégia envolveu:

Estratificação de Amostragem: Uma vez identificada que a classe majoritária representava 79% do dataset, a divisão de treino/teste utilizou estratificação (stratify=y) para garantir que os 20% de dados de teste contivessem a mesma exata proporção de inadimplentes e aprovados encontrados no mundo real.

Padronização Crítica (StandardScaler): O KNN depende inteiramente da distância geométrica multidimensional entre os pontos. Como a Renda Mensal possui valores absolutos na casa dos milhares (média de R$ 4.596) e as Dívidas Ativas são discretas e baixas (média de 1.99), a falta de normalização geraria um viés matemático onde a distância da renda anularia qualquer peso do score e das dívidas. Aplicou-se a normalização Z-score para colocar todas as variáveis na mesma escala simétrica centrada em zero.

# Quais padrões de acesso/perfis foram considerados normais ou propensos à aprovação pelo modelo?

O modelo mapeou que perfis com alto score de crédito (médias próximas ou superiores a 664) e baixo número de dívidas ativas (abaixo de 2) concentram os vetores de aprovação do crédito. A renda mensal não atuou como um isolador linear absoluto, indicando que clientes de renda moderada mas com bom comportamento de pagamentos (alto score) têm caminhos facilitados para aprovação.

# Quais características aparecem com maior frequência nos acessos/perfis classificados como não aprovados?

Os perfis que receberam a classificação 0 (Não Aprovado) caracterizam-se por scores de crédito severamente deprimidos (médias inferiores a 600) e acumulação de dívidas ativas em aberto (médias superiores a 2.6).

# Analisar os resultados obtidos e comentar sobre o desempenho do modelo (Acurácia, Precisão, Recall e F1-Score).

A acurácia global do modelo foi de 75.00% no conjunto de testes. No entanto, uma análise preditiva em dados reais exige olhar além da acurácia devido ao desbalanceamento das classes:

Para a Classe 1 (Aprovados): O modelo obteve excelente desempenho, com Precision de 79% e Recall de 94% (F1-score de 0.86). Isso significa que quando o modelo diz que um crédito está aprovado, há boa confiabilidade, e ele consegue capturar quase todos os clientes bons da base.

Para a Classe 0 (Não Aprovados): O modelo registrou métricas nulas no conjunto de testes padrão de 20 linhas devido à escassez extrema de dados negativos na amostra fina de teste (apenas 4 exemplos negativos disponíveis). Como o espaço vetorial de treino continha poucos exemplos de reprovação, os 5 vizinhos mais próximos de qualquer ponto de teste acabavam sendo dominados por pontos da classe majoritária (Aprovados), mascarando as reprovações.

# O papel da Normalização de Dados no KNN.

A normalização não é opcional para o KNN; ela define se o modelo funcionará ou falhará. Sem a padronização, o modelo se tornaria um "classificador de renda disfarçado", ignorando se o cliente possui 8 dívidas ativas ou score zero, gerando prejuízos massivos para a instituição financeira por conceder crédito a perfis de altíssimo risco com rendas altas distorcidas.

# Como esse tipo de modelo poderia ser usado em um sistema real?

Em uma fintech ou banco tradicional, este modelo funcionaria como o mecanismo de triagem automática (Esteira de Crédito Expressa):

Propostas que entram via aplicativo e recebem predição 1 com 100% de concordância dos vizinhos recebem aprovação instantânea via API.

Propostas que entram e recebem predição 0 ou que caem em zonas cinzentas de votação dividida (ex: 3 vizinhos dizem sim, 2 dizem não) são desviadas automaticamente para a mesa de análise humana, poupando tempo operacional e blindando o banco contra calotes.
