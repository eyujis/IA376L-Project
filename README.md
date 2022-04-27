# `Geração de Imagens de Espaços Indoor`
# `Indoor Scenes Image Generation`

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

Nesse projeto, temos o objetivo de gerar imagens de espaços indoors a partir de um modelo sintético. Esse problema foi encontrado no paper Recognizing Indoor Scenes [2], onde é apresentado que a tarefa de reconhecimento de ambientes internos pode ser desafiadora no contexto de visão computacional. Dessa forma, queremos criar imagens inseridas nesse contexto e assim medir a capacidade do modelo de geração de ambientes arquitetônicamente criativos e realistas que podem ser utilizados como inspiração para projetos de design de interiores.

<a href="https://www.youtube.com/watch?v=v-esoYPAqTc" target="_blank">Vídeo da apresentação da proposta do projeto</a>

## Metodologia Proposta
> Para a primeira entrega, a metodologia proposta deve esclarecer:
> * Qual(is) base(s) de dado(s) o projeto pretende utilizar, justificando a(s) escolha(s) realizadas.

Utilizaremos o [Indoor Scene Recognition dataset](http://web.mit.edu/torralba/www/indoor.html). Ele contém 67 categorias de espaços indoor, divididos em 5 grandes grupos (Store, Home, Public spaces, Leisure, Working Place), e um total de 15620 imagens. O número de imagens varia entre categorias, porém cada uma possui pelo menos 100 exemplos. Todas as imagens estão em formato jpg. Cada imagem tem uma resolução mínima de 200 pixels em seu menor eixo.

![dataset_sample](http://web.mit.edu/torralba/www/allIndoors.jpg)

> * Quais abordagens de modelagem generativa o grupo já enxerga como interessantes de serem estudadas.

Primeiro, utilizamos uma Generative Adversarial Nets (GAN) e um Variational Autoencoder (VAE) [4] para a geração de imagens sem categoria especificada. Depois evoluiremos nossos modelos para uma Conditional GAN (CGAN) e um Conditional VAE (CVAE) [5] onde geraremos imagens especificando um grande grupo, dentre os 5, ao qual a imagem gerada deva pertencer.

> * Artigos de referência já identificados e que serão estudados ou usados como parte do planejamento do projeto
> * Ferramentas a serem utilizadas (com base na visão atual do grupo sobre o projeto).

Pytorch, Google Colab

> * Proposta de avaliação

Utilizaremos a _nearest neighbor Adversarial Accuracy_ (AA) para calcular a _Privacy Loss_ (AA_test - AA_train) que verifica se o modelo está simplesmente copiando as imagens do conjunto de treino, indo contra as premissas do projeto de criar novas imagens. Além disso, mensuraremos a _Utility_ do modelo, para isso, treinaremos dois classificadores (de grandes grupos) com parâmetros idênticos, um com dados reais e outro com dados sintéticos, e compararemos a acurácia de ambos os modelos classificando um conjunto de teste de imagens reais. As métricas acima são introduzidas em [8].

Também utilizaremos a Fréchet Inception Distance (FID), introduzida em [9], que transforma amostras sintetizadas num feature vector especificado por uma camada da Inception Net. Analisando este embedding como uma gaussiana multivariada, a média e a covariância são calculadas para os dados sintéticos e para os dados reais. A distância de Fréchet entre essas duas gaussianas (também conhecida como distância de Wasserstein-2) é usada para quantificar a qualidade das amostras sintetizadas. Um menor FID significa uma menor distância entre as distribuições de dados sintéticos e reais. 

> * Resultados esperados

Do ponto de vista qualitativo, esperamos que os modelos baseados em GANs gerem imagens mais nítidas, com bordas melhores definidas do que as imagens geradas pelos modelos baseados em VAEs. Isso deve se refletir, para esses modelos, em uma melhor métrica de Fréchet.

Do ponto de vista da capacidade dos modelos em gerar imagens criativas, esperamos uma baixa performance, de maneira em que as imagens geradas sejam extremamente semelhantes aos exemplos de treino, resultando numa Privacy Loss alta [8].

Na visão de utilidade do dataset gerado, esperamos que as amostras sintéticas sejam capazes de treinar um modelo classificador para as imagens com performance semelhante a um modelo treinado com dados reais. Esperamos, novamente, que as soluções baseadas em GANs tenham uma performance superior às baseadas em VAEs.

## Cronograma
> Proposta de cronograma. Procure estimar quantas semanas serão gastas para cada etapa do projeto.

|Início|Tarefa|Duração|
|-|-|-|
| 20/04 | Elaboração da proposta | 1 semana
| 20/04 | Estudo sobre métricas de similaridade entre imagens | 6 semanas
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
|Estudo sobre métricas de similaridade entre imagens|X|X|X|X|X|X||||||||||
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

[2] [Quattoni, A., & Torralba, A. (2009, June). Recognizing indoor scenes. In 2009 IEEE conference on computer vision and pattern recognition (pp. 413-420). IEEE.](http://people.csail.mit.edu/torralba/publications/indoor.pdf)

[3] [Goodfellow, I., Pouget-Abadie, J., Mirza, M., Xu, B., Warde-Farley, D., Ozair, S., ... & Bengio, Y. (2014). Generative adversarial nets. Advances in neural information processing systems, 27.](https://proceedings.neurips.cc/paper/2014/hash/5ca3e9b122f61f8f06494c97b1afccf3-Abstract.html)

[4] [Kingma, D. P., & Welling, M. (2013). Auto-encoding variational bayes. arXiv preprint arXiv:1312.6114.](https://arxiv.org/abs/1312.6114)

[5] [Sohn, K., Lee, H., & Yan, X. (2015). Learning structured output representation using deep conditional generative models. Advances in neural information processing systems, 28.](https://papers.nips.cc/paper/2015/file/8d55a249e6baa5c06772297520da2051-Paper.pdf)

[6] [Naeem, M. F., Oh, S. J., Uh, Y., Choi, Y., & Yoo, J. (2020, November). Reliable fidelity and diversity metrics for generative models. In International Conference on Machine Learning (pp. 7176-7185). PMLR.](http://proceedings.mlr.press/v119/naeem20a/naeem20a.pdf)

[7] [Borji, A. (2019). Pros and cons of gan evaluation measures. Computer Vision and Image Understanding, 179, 41-65.](https://arxiv.org/pdf/1802.03446.pdf)

[8] [Yale, A., Dash, S., Dutta, R., Guyon, I., Pavao, A., & Bennett, K. P. (2020). Generation and evaluation of privacy preserving synthetic health data. Neurocomputing, 416, 244-255.](https://www.sciencedirect.com/science/article/pii/S0925231220305117)

[9] [Heusel, M., Ramsauer, H., Unterthiner, T., Nessler, B., & Hochreiter, S. (2017). Gans trained by a two time-scale update rule converge to a local nash equilibrium. Advances in neural information processing systems, 30.](https://proceedings.neurips.cc/paper/2017/hash/8a1d694707eb0fefe65871369074926d-Abstract.html)


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
