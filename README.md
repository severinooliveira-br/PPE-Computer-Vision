# Detecção e Segmentação de Pessoas e Equipamentos de Proteção Individual

Projeto de Visão Computacional desenvolvido no contexto de um curso de Engenharia de IA, com foco na aplicação de técnicas de detecção de objetos e segmentação de instâncias para identificação de pessoas e Equipamentos de Proteção Individual (EPIs) em imagens de ambientes de trabalho.

## 1. Objetivo

Desenvolver e avaliar uma solução de Visão Computacional capaz de identificar pessoas e equipamentos de proteção individual em imagens e vídeos relacionados à segurança do trabalho.

O projeto utiliza modelos da família YOLO, realizando duas tarefas:

- Detecção de objetos;
- Segmentação de instâncias.

A solução foi desenvolvida de forma experimental e acadêmica, buscando demonstrar o fluxo completo de um projeto de Visão Computacional: aquisição dos dados, análise exploratória, preparação, treinamento, avaliação e inferência em vídeo.

---

## 2. Cenário

O cenário escolhido foi **Segurança do Trabalho**, com foco na identificação visual de pessoas e equipamentos utilizados em ambientes de trabalho.

A aplicação de Visão Computacional nesse contexto pode contribuir para sistemas de apoio ao monitoramento de condições de segurança, permitindo a identificação automática de elementos relacionados ao uso de EPIs.

O projeto não tem como objetivo substituir procedimentos de segurança ou avaliações realizadas por profissionais habilitados.

---

## 3. Classes utilizadas

O conjunto de dados possui cinco classes:

| ID | Classe |
|---:|---|
| 0 | person |
| 1 | with-helmet |
| 2 | with-suit |
| 3 | without-helmet |
| 4 | without-suit |

---

## 4. Dataset

Foi utilizado o dataset **PPE**, disponibilizado publicamente na plataforma Roboflow Universe.

Fonte:

https://universe.roboflow.com/ppe-656ud/ppe-fq2dk

Versão utilizada:

**PPE v6 – Instance Segmentation**

O dataset original possui:

- 1.736 imagens;
- 1.617 imagens de treinamento;
- 75 imagens de validação;
- 44 imagens de teste;
- resolução de 640 × 640 pixels;
- anotações para detecção e segmentação;
- licença CC BY 4.0.

Durante o controle de qualidade foram identificadas quatro imagens com arquivos de anotação vazios, embora apresentassem pessoas/EPIs visualmente relevantes.

Essas imagens foram removidas do conjunto experimental para evitar que fossem tratadas como exemplos negativos.

Após a limpeza:

- Treinamento: 1.614 imagens;
- Validação: 74 imagens;
- Teste: 44 imagens.

O conjunto de teste não apresentou o problema de anotações vazias identificado durante o controle de qualidade.

---

## 5. Análise exploratória dos dados

O dataset contém **10.459 objetos anotados**.

Distribuição das classes:

| Classe | Objetos | Percentual |
|---|---:|---:|
| person | 3.541 | 33,86% |
| with-suit | 3.101 | 29,65% |
| with-helmet | 2.466 | 23,58% |
| without-helmet | 1.079 | 10,32% |
| without-suit | 272 | 2,60% |

Foi observada uma distribuição desigual entre as classes. A classe `person` apresenta a maior quantidade de ocorrências, enquanto `without-suit` apresenta a menor.

A razão entre a classe mais frequente e a menos frequente é aproximadamente 13:1.

Também foram analisadas características como:

- quantidade de objetos por imagem;
- resolução das imagens;
- distribuição de brilho;
- distribuição das classes;
- existência de arquivos de anotação;
- correspondência entre imagens e anotações.

Uma inspeção visual qualitativa de uma amostra aleatória de 20 imagens do conjunto de treinamento foi realizada. As máscaras analisadas apresentaram posicionamento e correspondência adequados aos objetos presentes nas imagens, sem identificação de erros de marcação na amostra avaliada.

---

## 6. Preparação dos dados

As anotações originais do dataset estão no formato utilizado pelo YOLO para segmentação de instâncias, contendo a classe e os pontos que formam o polígono da máscara.

Para o treinamento do modelo de detecção, os polígonos foram convertidos para caixas delimitadoras no formato YOLO:
classe x_centro y_centro largura altura

As coordenadas foram normalizadas entre 0 e 1.
Foram realizadas validações para verificar:
- correspondência entre imagens e arquivos de anotação;
- quantidade de valores por anotação;
- IDs das classes;
- coordenadas normalizadas;
- ausência de erros nos arquivos processados.

## 7. Modelos
Foi utilizada a biblioteca Ultralytics e modelos da família YOLO11.

# Detecção
Modelo utilizado:
YOLO11n
Configuração principal:
- Épocas: 30
- Imagem: 640 × 640
- Batch: 16
- GPU: Tesla T4
- Seed: 42
- Patience: 10

# Segmentação
Modelo utilizado:
YOLO11n-seg
Configuração principal:
- Épocas: 30
- Imagem: 640 × 640
- Batch: 16
- GPU: Tesla T4
- Seed: 42
- Patience: 10

Os resultados finais da segmentação serão consolidados após a execução definitiva do treinamento e avaliação.

## 8. Resultado preliminar — Detecção
O modelo YOLO11n foi treinado durante 30 épocas.

Resultado no conjunto de teste:
Métrica	      Resultado
Precision	    0,906
Recall	      0,937
mAP@0.5	      0,960
mAP@0.5:0.95  0,783

F1-score por classe:
Classe	        F1
person	        0,975
with-helmet	    0,888
with-suit	      0,931
without-helmet	0,999
without-suit	  0,810

Os resultados indicam diferenças de desempenho entre as classes, principalmente nas classes com menor quantidade de exemplos.
A classe without-suit, por exemplo, possui a menor quantidade de objetos anotados e apresentou desempenho inferior às demais classes.

## 9. Avaliação
A avaliação do projeto considera as seguintes métricas:
- Precision;
- Recall;
- F1-score;
- mAP@0.5;
- mAP@0.5:0.95;
- IoU;
- matriz de confusão;
- análise qualitativa de falsos positivos e falsos negativos.
- 
O IoU (Intersection over Union) é utilizado para medir a sobreposição entre a região prevista pelo modelo e a região correspondente ao objeto real.
A avaliação consolidada dos modelos será realizada no Notebook 05.

## 10. Inferência em vídeo
O Notebook 06 será utilizado para demonstrar a aplicação dos modelos em um vídeo real com duração mínima de 30 segundos.
A demonstração deverá apresentar a identificação dos objetos pelo modelo e, no caso da segmentação, as respectivas máscaras.
A aplicação em vídeo representa a etapa final do fluxo experimental:

Imagem
   ↓
Pré-processamento
   ↓
Modelo YOLO
   ↓
Detecção / Segmentação
   ↓
Visualização dos resultados
   ↓
Vídeo processado

## 11. Organização do projeto
PPE-Computer-Vision/
│
├── 01_EDA_PPE_v6.ipynb
├── 02_Preparacao_Dados.ipynb
├── 03_Treinamento_Deteccao.ipynb
├── 04_Treinamento_Segmentacao.ipynb
├── 05_Avaliacao.ipynb
├── 06_Inferencia_Video.ipynb
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── report/
│   └── Relatorio_Tecnico_PPE.md
│
├── models/
│
└── results/

# Fluxo dos notebooks
01 - EDA
  ↓
02 - Preparação dos dados
  ↓
03 - Treinamento da detecção
  ↓
04 - Treinamento da segmentação
  ↓
05 - Avaliação
  ↓
06 - Inferência em vídeo

## 12. Reprodutibilidade
Os experimentos foram desenvolvidos utilizando Google Colab com GPU NVIDIA Tesla T4.
Principais tecnologias:
- Python;
- Google Colab;
- Ultralytics;
- YOLO11;
- OpenCV;
- NumPy;
- Matplotlib;
- PyTorch.
As instruções de instalação e dependências estão disponíveis no arquivo requirements.txt.

Os notebooks foram organizados para permitir a reprodução das principais etapas do experimento.
Os arquivos de pesos dos modelos (.pt) não são armazenados diretamente neste repositório.

## 13. Limitações
Entre as principais limitações identificadas estão:
- desequilíbrio na quantidade de exemplos entre as classes;
- menor quantidade de exemplos para a classe without-suit;
- possibilidade de erros ou ausência de objetos nas anotações originais;
- influência das condições de iluminação e perspectiva;
- diferença entre o ambiente representado no dataset e ambientes reais;
- avaliação realizada sobre um conjunto de dados específico.
Os resultados não devem ser interpretados como garantia de desempenho em qualquer ambiente de segurança do trabalho.

## 14. Próximas etapas
As próximas etapas do projeto são:
  1. Finalizar o treinamento da segmentação;
  2. Consolidar os resultados do modelo de segmentação;
  3. Implementar a avaliação completa;
  4. Calcular explicitamente o IoU;
  5. Realizar análise de falsos positivos e falsos negativos;
  6. Comparar detecção e segmentação;
  7. Executar inferência em vídeo;
  8. Consolidar o relatório técnico;
  9. Preparar a apresentação final.

## 15. Uso de Inteligência Artificial
Ferramentas de Inteligência Artificial foram utilizadas como apoio durante o desenvolvimento do projeto, principalmente para:
- esclarecimento de conceitos;
- organização das etapas do projeto;
- auxílio na documentação;
- revisão de código;
- interpretação de métricas;
- apoio na identificação de possíveis problemas.
A execução dos experimentos, preparação dos dados, treinamento dos modelos e análise dos resultados foram realizadas no ambiente de desenvolvimento do projeto.

## 16. Referências
# Dataset
Roboflow Universe — PPE Dataset.
https://universe.roboflow.com/ppe-656ud/ppe-fq2dk
# Ultralytics
Ultralytics — YOLO.
https://docs.ultralytics.com/
# YOLO
Ultralytics YOLO — documentação e implementação dos modelos utilizados no projeto.
https://github.com/ultralytics/ultralytics

Status do projeto
Em desenvolvimento
Etapas concluídas:
- [x] Definição do cenário
- [x] Seleção do dataset
- [x] Análise exploratória
- [x] Controle de qualidade dos dados
- [x] Preparação dos dados
- [x] Treinamento do modelo de detecção
- [x] Treinamento inicial do modelo de segmentação
Etapas em andamento:
- [ ] Consolidação da segmentação
- [ ] Avaliação final
- [ ] Análise de FP/FN
- [ ] Inferência em vídeo
- [ ] Relatório final
- [ ] Apresentação
