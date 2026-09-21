Relatório Técnico — Detecção e Segmentação de Pessoas e EPI
Projeto: Detecção e Segmentação de Pessoas e Equipamentos de Proteção Individual utilizando Visão Computacional  
Cenário: Segurança do Trabalho / Construção Civil  
Framework: Ultralytics YOLO  
Dataset: PPE Dataset v6 — Roboflow Universe  
Status: versão de trabalho para consolidação final
---
1. Introdução
Este projeto aplica técnicas de visão computacional ao cenário de segurança do trabalho, com foco na detecção e segmentação de pessoas e de categorias visuais relacionadas ao uso de equipamentos de proteção individual (EPI).
A proposta contempla um fluxo completo de desenvolvimento de uma solução de visão computacional: análise exploratória do dataset, controle de qualidade, preparação das anotações, treinamento de modelos, avaliação quantitativa, análise qualitativa e inferência em vídeo.
Foram utilizadas duas abordagens complementares:
detecção de objetos, utilizando YOLO11n;
segmentação de instâncias, utilizando YOLO11n-seg.
O projeto tem caráter acadêmico e experimental. Os resultados obtidos representam o comportamento dos modelos no dataset utilizado e não devem ser interpretados como validação de um sistema operacional de segurança.
---
2. Problema e cenário
2.1 Definição do problema
Em ambientes de construção e atividades operacionais, pessoas podem utilizar diferentes equipamentos de proteção individual. A identificação automática desses elementos em imagens e vídeos pode servir como apoio a processos de supervisão e análise.
O problema computacional foi definido como:
> **Detectar e segmentar pessoas e categorias visuais relacionadas ao uso de equipamentos de proteção individual em imagens e vídeos.**
2.2 Classes
O dataset utilizado possui cinco classes:
ID	Classe
0	person
1	with-helmet
2	with-suit
3	without-helmet
4	without-suit
Algumas classes podem aparecer simultaneamente na mesma pessoa. Portanto, a matriz de confusão e as métricas devem ser interpretadas considerando a natureza multilabel/visualmente sobreposta do problema.
2.3 Aplicação pretendida
A aplicação experimental é voltada à análise de imagens e vídeos de ambientes de trabalho.
Uma solução futura poderia apoiar processos de supervisão, geração de alertas ou análise retrospectiva. Entretanto, qualquer utilização operacional exigiria dados adicionais, validação no ambiente real e avaliação específica de segurança.
---
3. Dataset e EDA
3.1 Fonte
Foi utilizado o PPE Dataset v6, disponibilizado no Roboflow Universe.
Fonte: Roboflow Universe — PPE Dataset, versão 6  
URL: https://universe.roboflow.com/ppe-656ud/ppe-fq2dk/dataset/6  
Licença: CC BY 4.0
O dataset original possui 1.736 imagens:
Conjunto	Imagens	Percentual
Treino	1.617	93%
Validação	75	4%
Teste	44	3%
Total	1.736	100%
Todas as imagens utilizadas pelo dataset estão em resolução de 640 × 640 pixels.
3.2 Formato das anotações
As anotações originais são poligonais, adequadas à segmentação de instâncias no formato YOLO.
Para o experimento de detecção, os polígonos foram convertidos em bounding boxes normalizadas no formato YOLO.
3.3 Pré-processamento disponibilizado
Segundo os metadados do dataset:
resize/stretch para 640 × 640;
três saídas por exemplo de treinamento;
flip horizontal;
saturação de -25% a +25%;
brilho de -15% a +15%;
blur de até 1 pixel.
3.4 Controle de qualidade
Durante a análise foram identificados quatro arquivos de anotação vazios:
três no treinamento;
um na validação;
nenhum no teste.
As imagens correspondentes apresentavam pessoas visualmente relevantes. Para evitar tratá-las como exemplos negativos, foram removidas do conjunto experimental.
Após a limpeza:
Conjunto	Imagens
Treino	1.614
Validação	74
Teste	44
O conjunto passou a conter 10.459 objetos anotados.
3.5 Distribuição das classes
Classe	Objetos	Participação
person	3.541	33,86%
with-suit	3.101	29,65%
with-helmet	2.466	23,58%
without-helmet	1.079	10,32%
without-suit	272	2,60%
A razão entre a classe mais frequente e a menos frequente é de aproximadamente 13,02:1, evidenciando desequilíbrio entre classes.
3.6 Resolução e luminosidade
As imagens possuem 640 × 640 pixels.
A análise de intensidade em escala de cinza apresentou:
Estatística	Valor
Mínimo	66,25
Q1	102,35
Mediana	113,69
Média	113,17
Q3	122,11
Máximo	160,30
Desvio-padrão	18,41
3.7 Objetos por imagem
Estatística	Valor
Média	6,02
Mediana	6
Q1	3
Q3	9
Mínimo	0
Máximo	18
3.8 Inspeção das anotações
Foi realizada inspeção visual de uma amostra aleatória de 20 imagens do conjunto de treinamento.
As máscaras analisadas apresentaram posicionamento e correspondência adequados aos objetos presentes nas imagens, sem identificação de erros de marcação na amostra avaliada.
Também foram visualizadas amostras das bounding boxes produzidas pela conversão dos polígonos.
A inspeção foi amostral e, portanto, não permite afirmar que todas as anotações do dataset sejam perfeitas.
---
4. Metodologia
4.1 Fluxo experimental
O projeto foi organizado em seis notebooks:
```text
01_EDA
   ↓
02_Preparação dos dados
   ↓
03_Treinamento da detecção
   ↓
04_Treinamento da segmentação
   ↓
05_Avaliação
   ↓
06_Inferência em vídeo
```
4.2 Preparação para detecção
As anotações poligonais foram convertidas para bounding boxes.
Para cada polígono foram determinados:
X mínimo;
X máximo;
Y mínimo;
Y máximo;
largura;
altura;
centro X;
centro Y.
As coordenadas foram normalizadas para o intervalo [0,1].
A validação automática verificou:
correspondência entre imagens e labels;
quantidade de valores;
IDs de classe;
intervalo das coordenadas.
A validação final apresentou zero erros.
4.3 Modelo de detecção
Foi utilizado o YOLO11n, inicializado com pesos pré-treinados.
Parâmetro	Valor
Modelo	YOLO11n
Épocas	30
Imagem	640 × 640
Batch	16
Seed	42
Patience	10
Dispositivo	NVIDIA Tesla T4
Framework	Ultralytics 8.4.157
4.4 Modelo de segmentação
Foi utilizado o YOLO11n-seg, utilizando as anotações poligonais originais.
Configuração planejada:
Parâmetro	Valor
Modelo	YOLO11n-seg
Épocas	30
Imagem	640 × 640
Batch	16
Seed	42
Patience	10
Dispositivo	NVIDIA Tesla T4
Framework	Ultralytics 8.4.157
Status: resultado definitivo a consolidar após a reexecução do treinamento.
---
5. Resultados — Detecção
5.1 Validação
O modelo foi validado em 74 imagens e 400 instâncias.
Métrica	Resultado
Precision	93,7%
Recall	89,8%
mAP@0.5	94,6%
mAP@0.5:0.95	77,6%
5.2 Resultado por classe — validação
Classe	Precision	Recall	mAP@0.5	mAP@0.5:0.95
person	94,6%	91,5%	95,2%	84,2%
with-helmet	93,0%	87,3%	92,5%	64,7%
with-suit	95,0%	89,9%	96,6%	81,1%
without-helmet	92,5%	88,0%	91,0%	75,5%
without-suit	93,3%	92,4%	97,5%	82,5%
5.3 Teste
A avaliação final do detector utilizou 44 imagens e 242 instâncias.
Métrica	Resultado
Precision	90,6%
Recall	93,7%
mAP@0.5	96,0%
mAP@0.5:0.95	78,3%
5.4 F1-score
O F1-score foi calculado por:
F1 = 2 × Precision × Recall / (Precision + Recall)
Classe	F1
person	0,975
with-helmet	0,888
with-suit	0,931
without-helmet	0,999
without-suit	0,810
---
6. Resultados — Segmentação
Esta seção será preenchida após a execução definitiva do YOLO11n-seg.
Deverão ser registrados:
Mask Precision;
Mask Recall;
Mask mAP@0.5;
Mask mAP@0.5:0.95;
IoU;
métricas por classe;
matriz de confusão;
exemplos qualitativos.
A segmentação possui objetivo diferente da detecção: enquanto a detecção localiza objetos por bounding boxes, a segmentação procura delimitar a região ocupada por cada instância.
---
7. IoU
Intersection over Union (IoU) é definida por:
IoU = Área da interseção / Área da união
No projeto:
detecção: IoU entre bounding boxes;
segmentação: IoU entre máscaras.
As métricas mAP utilizam critérios baseados em IoU para determinar correspondências entre previsões e ground truth.
A avaliação final deverá apresentar uma medida explícita de IoU, conforme o requisito da atividade.
---
8. Análise de erros
8.1 Falsos positivos
Devem ser investigadas situações em que o modelo identifica uma classe que não corresponde ao objeto anotado.
Possíveis situações:
pessoas muito pequenas;
oclusões;
objetos do cenário visualmente semelhantes;
classes visualmente próximas.
8.2 Falsos negativos
Devem ser investigados casos em que objetos presentes não foram identificados.
Possíveis situações:
distância;
oclusão;
baixa iluminação;
objetos parcialmente fora do quadro;
EPI pouco visível.
8.3 Análise qualitativa já realizada
Foram visualizados lotes de validação contendo ground truth e previsões.
Nas amostras analisadas não foram identificados erros grosseiros de detecção ou segmentação.
Essa observação é limitada às amostras visualizadas e não significa ausência de erros no conjunto inteiro.
8.4 Desequilíbrio
A classe `without-suit` possui apenas 272 instâncias, correspondendo a 2,60% dos objetos anotados.
Esse desequilíbrio deve ser considerado na interpretação das métricas e em eventuais melhorias futuras.
---
9. Limitações
O conjunto de teste possui somente 44 imagens.
Existe desequilíbrio significativo entre as classes.
Foram encontradas quatro imagens com conteúdo visual relevante e anotações vazias.
A inspeção manual das anotações foi amostral.
O dataset pode não representar todas as condições reais de uma obra.
O desempenho pode variar com iluminação, câmera, distância, resolução e oclusão.
O modelo ainda deve ser validado no vídeo real previsto para a demonstração.
A solução não deve ser utilizada como único mecanismo de decisão em segurança.
---
10. Próximos passos
finalizar o treinamento de segmentação;
executar avaliação no conjunto de teste;
calcular IoU;
consolidar matriz de confusão;
selecionar exemplos de FP e FN;
executar inferência em vídeo de pelo menos 30 segundos;
medir duração e, se possível, FPS;
consolidar relatório final;
publicar o código organizado no GitHub.
Como evolução futura, podem ser investigados tracking com ByteTrack, aumento da diversidade do dataset, novas arquiteturas, dados reais do ambiente e otimização de inferência.
---
11. Conclusão
O projeto estabeleceu um fluxo completo de visão computacional aplicado à segurança do trabalho, abrangendo análise de dados, preparação, treinamento e avaliação.
O detector YOLO11n apresentou no conjunto de teste Precision de 90,6%, Recall de 93,7%, mAP@0.5 de 96,0% e mAP@0.5:0.95 de 78,3%.
Esses valores representam o desempenho no dataset experimental e não devem ser generalizados diretamente para ambientes reais.
A etapa de segmentação e a demonstração em vídeo completarão a avaliação da solução e permitirão demonstrar o comportamento do sistema em um cenário temporal.
---
12. Referências
Roboflow Universe. PPE Dataset — Version 6. https://universe.roboflow.com/ppe-656ud/ppe-fq2dk/dataset/6. Licença CC BY 4.0.
Ultralytics. Ultralytics YOLO Documentation.
Notebooks e código desenvolvidos no próprio projeto.
Declaração de uso de IA
Ferramentas de Inteligência Artificial generativa foram utilizadas como apoio ao desenvolvimento, principalmente para esclarecimento de conceitos, organização da documentação, revisão de código e estruturação das etapas. O código foi executado e os resultados foram verificados no ambiente Google Colab.
