# `<Título em Português do Projeto>`
# `<Project Title in in English>`

## Apresentação

O presente projeto foi originado no contexto das atividades da disciplina de pós-graduação *IA376L - Deep Learning aplicado a Síntese de Sinais*, 
oferecida no primeiro semestre de 2022, na Unicamp, sob supervisão da Profa. Dra. Paula Dornhofer Paro Costa, do Departamento de Engenharia de Computação e Automação (DCA) da Faculdade de Engenharia Elétrica e de Computação (FEEC).

> Incluir nome RA e foco de especialização de cada membro do grupo. Os grupos devem ter no máximo três integrantes.
> |Nome  | RA | Especialização|
> |--|--|--|
> | Eduardo Yuji Sakabe | 166810  | Eng. de Computação|
> | Hélder Jesus Vieira  | 169496  | Eng. Eletricisita|
> | Leonardo Bernardi de Avila  | 224016  | Eng. Eletricista |


## Descrição Resumida do Projeto
> Descrição do tema do projeto, incluindo contexto gerador, motivação.
> Descrição do objetivo principal do projeto.
> Esclarecer qual será a saída do modelo de síntese, ou generativo.
> 
> Incluir nessa seção link para vídeo de apresentação da proposta do projeto (máximo 5 minutos).

Nesse projeto, temos o objetivo de gerar imagens de espaços indoors a partir de um modelo sintético. Esse problema foi encontrado no paper [PAPER], onde é apresentado que a tarefa de reconhecimento de ambientes internos pode ser desafiadora no contexto de visão computacional. Isso se dá, pois em ambientes internos temos diferentes características que podem ajudar a caracterizar o ambiente, por exemplo, imagens de corredores podem ser bem caracterizadas por propriedades espaciais enquanto que uma biblioteca é melhor caracterizada pelos objetos que ela contém. Com isso, podemos medir a capacidade do modelo de geração de ambientes arquitetônicamente criativos que podem ser utilizados como inspiração para projetos de design de interiores.

## Metodologia Proposta
> Para a primeira entrega, a metodologia proposta deve esclarecer:
> * Qual(is) base(s) de dado(s) o projeto pretende utilizar, justificando a(s) escolha(s) realizadas.

Utilizaremos o [Indoor Scene Recognition dataset](http://web.mit.edu/torralba/www/indoor.html). Ele contém 67 categorias de espaços indoor, divididos em 5 grandes grupos (Store, Home, Public spaces, Leisure, Working Place), e um total de 15620 imagens. O número de imagens varia entre categorias, porém cada uma possui pelo menos 100 exemplos. Todas as imagens estão em formato jpg. Cada imagem tem uma resolução mínima de 200 pixels em seu menor eixo.

![dataset_sample](http://web.mit.edu/torralba/www/allIndoors.jpg)

> * Quais abordagens de modelagem generativa o grupo já enxerga como interessantes de serem estudadas.

Primeiro, utilizamos uma Generative Adversarial Nets (GAN) e um Variational Autoencoder (VAE) para a geração de imagens sem categoria especificada. Depois evoluiremos nossos modelos para uma Conditional GAN (CGAN) e um Conditional VAE (CVAE) onde geraremos imagens especificando um grande grupo, dentre os 5, ao qual a imagem gerada deva pertencer.

> * Artigos de referência já identificados e que serão estudados ou usados como parte do planejamento do projeto
> * Ferramentas a serem utilizadas (com base na visão atual do grupo sobre o projeto).

Pytorch, Google Colab

> * Resultados esperados
> * Proposta de avaliação

## Cronograma
> Proposta de cronograma. Procure estimar quantas semanas serão gastas para cada etapa do projeto.

|Início|Tarefa|Duração|
|-|-|-|
| 20/04 | Elaboração da proposta | 1 semana
| 20/04 | Estudos artigos | 4 semanas
| 27/04 | Estruturação do dataset | 1 semana
| 27/04 | Implementação da GAN | 3 semanas
| 27/04 | Implementação da VAE | 3 semanas
| 18/05 | Implementação da CGAN | 3 semanas
| 18/05 | Implementação da CVAE | 3 semanas
| 01/06 | Implementação da métrica Frechet | 2 semanas
| 01/06 | Implementação da métrica Adversarial Accuracy | 2 semanas
| 15/06 | Refatoração do código | 2 semanas
| 15/06 | Elaboração da apresentação final | 2 semanas

|Tarefa|20/04|27/04|04/05|11/05|18/05|25/05|01/06|08/06|15/06|22/06|29/06|06/07|
|-|-|-|-|-|-|-|-|-|-|-|-|-|
|Elaboração da proposta|X|||||||||||||||
|Estudos artigos|X|X|X|X||||||||||||
|Estruturação do dataset||X|X|||||||||||||
|Implementação da GAN||X|X|X||||||||||||
|Implementação da VAE ||X|X|X||||||||||||
|Implementação da CGAN|||||X|X|X|||||||||
|Implementação da CVAE|||||X|X|X|||||||||
|Implementação da métrica Frechet|||||||X|X||||||||
|Implementação da métrica Adversarial Accuracy|||||||X|X||||||||
|Refatoração do código|||||||||X|X||||||
|Elaboração da apresentação final|||||||||X|X||||||

## Referências Bibliográficas
> Apontar nesta seção as referências bibliográficas adotadas no projeto.

[1] [Indoor Scene Dataset](http://web.mit.edu/torralba/www/indoor.html)

[2] [Indoor Scene Dataset Paper](http://people.csail.mit.edu/torralba/publications/indoor.pdf)

[3]

[4]

[5] [Conditional VAE](https://papers.nips.cc/paper/2015/file/8d55a249e6baa5c06772297520da2051-Paper.pdf)

[6] [Reliable Fidelity and Diversity Metrics for Generative Models](http://proceedings.mlr.press/v119/naeem20a/naeem20a.pdf)

[7] [Pros and Cons of GAN Evaluation Measures](https://arxiv.org/pdf/1802.03446.pdf)

[8] [Generation and evaluation of privacy preserving synthetic health data](https://www.sciencedirect.com/science/article/pii/S0925231220305117)


## Estrutura do Projeto

    ├── LICENSE
    ├── Makefile           <- Makefile with commands like `make data` or `make train`
    ├── README.md          <- The top-level README for developers using this project.
    ├── data
    │   ├── external       <- Data from third party sources.
    │   ├── interim        <- Intermediate data that has been transformed.
    │   ├── processed      <- The final, canonical data sets for modeling.
    │   └── raw            <- The original, immutable data dump.
    │
    ├── docs               <- A default Sphinx project; see sphinx-doc.org for details
    │
    ├── models             <- Trained and serialized models, model predictions, or model summaries
    │
    ├── notebooks          <- Jupyter notebooks. Naming convention is a number (for ordering),
    │                         the creator's initials, and a short `-` delimited description, e.g.
    │                         `1.0-jqp-initial-data-exploration`.
    │
    ├── references         <- Data dictionaries, manuals, and all other explanatory materials.
    │
    ├── reports            <- Generated analysis as HTML, PDF, LaTeX, etc.
    │   └── figures        <- Generated graphics and figures to be used in reporting
    │
    ├── requirements.txt   <- The requirements file for reproducing the analysis environment, e.g.
    │                         generated with `pip freeze > requirements.txt`
    │
    ├── setup.py           <- makes project pip installable (pip install -e .) so src can be imported
    ├── src                <- Source code for use in this project.
    │   ├── __init__.py    <- Makes src a Python module
    │   │
    │   ├── data           <- Scripts to download or generate data
    │   │   └── make_dataset.py
    │   │
    │   ├── features       <- Scripts to turn raw data into features for modeling
    │   │   └── build_features.py
    │   │
    │   ├── models         <- Scripts to train models and then use trained models to make
    │   │   │                 predictions
    │   │   ├── predict_model.py
    │   │   └── train_model.py
    │   │
    │   └── visualization  <- Scripts to create exploratory and results oriented visualizations
    │       └── visualize.py
    │
    └── tox.ini            <- tox file with settings for running tox; see tox.readthedocs.io

<p><small>Project based on the <a target="_blank" href="https://drivendata.github.io/cookiecutter-data-science/">cookiecutter data science project template</a>. #cookiecutterdatascience</small></p>
