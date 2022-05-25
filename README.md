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

## Resumo (Abstract)

> Resumo do objetivo, metodologia **e resultados** obtidos. Sugere-se máximo de 100 palavras.



## Descrição do Problema/Motivação

> Descrição do contexto gerador do tema do projeto. Motivação para abordar este tema de projeto.

Nesse projeto, temos o objetivo de gerar imagens de espaços indoors a partir de um modelo sintético. Esse problema foi encontrado no paper Recognizing Indoor Scenes [2], onde é apresentado que a tarefa de reconhecimento de ambientes internos pode ser desafiadora no contexto de visão computacional. Dessa forma, queremos criar imagens inseridas nesse contexto e assim medir a capacidade do modelo de geração de ambientes arquitetônicamente criativos e realistas que podem ser utilizados como inspiração para projetos de design de interiores.

## Objetivo

> Descrição do que o projeto se propõe a fazer.
> É possível explicitar um objetivo geral e objetivos específicos do projeto.

## Metodologia Proposta

> Descrever de maneira clara e objetiva, citando referências, a metodologia proposta (E2) ou adotada (E3) para se alcançar os objetivos do projeto.
> Descrever bases de dados utilizadas.
> Citar algoritmos de referência.
> Justificar os porquês dos métodos escolhidos.
> Apontar ferramentas relevantes.
> Descrever metodologia de avaliação (como se avalia se os objetivos foram cumpridos ou não?).

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

## Resultados e Discussão dos Resultados

> Na entrega parcial do projeto (E2), essa seção pode conter resultados parciais, explorações de implementações realizadas e 
> discussões sobre tais experimentos, incluindo decisões de mudança de trajetória ou descrição de novos experimentos, como resultado dessas explorações.

> Na entrega final do projeto (E3), essa seção deve elencar os **principais** resultados obtidos (não necessariamente todos), que melhor representam o cumprimento
> dos objetivos do projeto.

> A discussão dos resultados pode ser realizada em seção separada ou integrada à sessão de resultados. Isso é uma questão de estilo.
> Considera-se fundamental que a apresentação de resultados não sirva como um tratado que tem como único objetivo mostrar que "se trabalhou muito".
> O que se espera da sessão de resultados é que ela **apresente e discuta** somente os resultados mais **relevantes**, que mostre os **potenciais e/ou limitações** da metodologia, que destaquem aspectos
> de **performance** e que contenha conteúdo que possa ser classificado como **compartilhamento organizado, didático e reprodutível de conhecimento relevante para a comunidade**. 

Do ponto de vista qualitativo, esperávamos inicialmente que os modelos baseados em GANs gerassem imagens mais nítidas, com bordas mais bem definidas do que as imagens geradas pelos modelos baseados em VAEs. Isso que esta suposição deveria se refletir, para esses modelos, em uma melhor métrica de Fréchet.

Do ponto de vista da capacidade dos modelos em gerar imagens criativas, esperávamos inicialmente uma baixa performance, de maneira em que as imagens geradas sejam extremamente semelhantes aos exemplos de treino, resultando numa Privacy Loss alta [8].

Na visão de utilidade do dataset gerado, esperávamos que as amostras sintéticas fossem capazes de treinar um modelo classificador para as imagens com performance semelhante a um modelo treinado com dados reais. Esperávamos também, novamente, que as soluções baseadas em GANs tenham uma performance superior às baseadas em VAEs.

Primeiro, implementamos a GAN utilizando o dataset com pre-processamento apresentado no notebook `notebooks/leonardo_dataprep.ipynb` com imagens de dimensões 28x28 nos canais RGB. Nossa implementação da GAN em PyTorch é uma adaptação da GAN do repositório [building-a-simples-vanilla-gan-with-pytorch](https://github.com/christianversloot/machine-learning-articles/blob/main/building-a-simple-vanilla-gan-with-pytorch.md). Realizamos dois experimentos utilizando uma GAN vanilla previamente validada no dataset MNIST com rede geradora de taxonomia 128x256x512 e rede discriminadora de taxonomia 1024x512x256 usando como função de loss entropia binária cruzada. Nos dois experimentos as funções de loss de ambas as redes não convergiram. 

Para o primeiro experimento no notebook `notebooks/GAN_full_dataset.ipynb` utilizamos o dataset inteiro com 15620 e treinamos o modelo para 50 épocas, a imagem abaixo são 16 samples geradas pela rede geradora após a última época. 

![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/GAN_sample_full_dataset_epoch_50.png)

Percebe-se que o modelo não foi capaz de gerar imagens de ambientes indoor. 

Logo, supomos que tivéssemos cometido uma falha de implementação, por isso treinamos novamente o modelo no notebook `notebooks/GAN_overfit_dataset.ipynb`, contudo com um dataset reduzido de apenas duas imagens. 

![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/GAN_overfit_dataset.png)

Durante o treinamento, o modelo foi capaz de reproduzir imagens semelhantes aos exemplos do dataset. 
Por exemplo, durante a época 209: 

![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/GAN_sample_overfit_dataset_epoch_209.png)

E durante a época 412:

![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/GAN_sample_overfit_dataset_epoch_412.png).

Logo, pudemos concluir que o nossa implementação estava correta, contudo nossa GAN não teve capacidade de generalização para uma maior diversidade de exemplos. Durante este processo, encontramos o repositório [GANs Indoor Scene Recognitionde](https://github.com/NVukobrat/GANs-Indoor-Scene-Recognition), com uma GAN treinada no mesmo dataset que o do nosso experimento. Os autores tiveram resultados semelhantes aos nossos, a GAN apenas alcança capacidade de replicar imagens de espaços indoor diretamente de exemplos de datasets pequenos, com menos de cinco images de uma mesma classe. Nosso grupo supõe que isso ocorre pelo fato de espaços indoores terem regularidades e padrões menos evidentes quando comparados ao [rosto humano](https://thispersondoesnotexist.com/image) e [números escritos à mão](https://machinelearningmastery.com/how-to-develop-a-generative-adversarial-network-for-an-mnist-handwritten-digits-from-scratch-in-keras/), ambos casos que possuem precedentes de sucesso com GANs. 

Dessa maneira, diferente de nosso planejamento inicial que tinha como próximo passo o desenvolvimento de um Conditional GAN (CGAN), iremos desenvolver uma Deep Convolutional GAN (DCGAN) para verificar se a estrutuda profunda de convoluções, propícia para geração de imagens de alta fidelidade, é capaz de generalizar mais exemplos distintos do dataset.

## Conclusão

> A sessão de Conclusão deve ser uma sessão que recupera as principais informações já apresentadas no relatório e que aponta para trabalhos futuros.
> Na entrega parcial do projeto (E2) pode conter informações sobre quais etapas ou como o projeto será conduzido até a sua finalização.
> Na entrega final do projeto (E3) espera-se que a conclusão elenque, dentre outros aspectos, possibilidades de continuidade do projeto.

E2: Devido aos resultados parciais apresentados até então, iremos inserir mais uma etapa de exploração de GANs e VAEs com estruturas mais complexas dos que os inicialmente abordados. Feito isso, serão então avaliados os modelos condicionais. Essas novas atividades deverão afetar, mas não atrasar, o cronograma proposto inicialmente.


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
