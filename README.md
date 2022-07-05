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

Este projeto tem como objetivo implementar e avaliar os modelos generativos Variational Autoencoder (VAE com camadas convolucionais), Generative Adversarial Networks (GAN), Deep Convolutional GAN (DCGAN), e FastGAN para a sintetização de imagens de espaços indoor da classe `bedroom`. Primeiro foi realizado uma avaliação qualitativa implementando todos os modelos e verificando quais seriam capazes de gerar figuras semelhantes a quartos. A FastGAN foi o único modelo gerou imagens qualitativamente interpretáveis, com elementos visuais de quartos, como abajures e camas. Desta maneira calculamos a Fréchet Inception Distance (FID), Adversarial Accuracy (AA) e a Privacy Loss deste modelo para avaliar, respectivamente, a qualidade das amostras sintetizadas e verificar se o modelo não estava simplesmente copiando imagens do dataset de treino. Os resultados obtidos indicaram uma baixa qualidade da imagem (AA >> 0.5), porém com baixa Privacy Loss, o que indica a ausência de cópia de imagens.  


## Descrição do Problema/Motivação

Nesse projeto, temos o objetivo de gerar imagens de espaços indoors a partir de um modelo sintético. Esse problema foi encontrado no paper Recognizing Indoor Scenes [2], onde é apresentado que a tarefa de reconhecimento de ambientes internos pode ser desafiadora no contexto de visão computacional. Dessa forma, queremos criar imagens inseridas nesse contexto e assim medir a capacidade do modelo de geração de ambientes arquitetônicamente criativos e realistas que podem ser utilizados como inspiração para projetos de design de interiores.

## Objetivo

Este projeto tem como objetivo geral implementar e avaliar os modelos generativos Variational Autoencoder (VAE com camadas convolucionais), Generative Adversarial Networks (GAN), Deep Convolutional GAN (DCGAN), e FastGAN para a tarefa de sintetização de imagens de espaços indoor da classe bedroom. Como objetivo específico, busca-se que o modelo sintetize exemplos diferentes dos presentes no treino, logo ele teria a capacidade de generalizar o conceito de quartos e não apenas replicar o que já teve acesso. 


## Metodologia Proposta

### Dataset
Utilizaremos o [Indoor Scene Recognition dataset](http://web.mit.edu/torralba/www/indoor.html). Ele contém 67 categorias de espaços indoor, divididos em 5 grandes grupos (Store, Home, Public spaces, Leisure, Working Place), e um total de 15620 imagens. O número de imagens varia entre categorias, porém cada uma possui pelo menos 100 exemplos. Todas as imagens estão em formato jpg. Cada imagem tem uma resolução mínima de 200 pixels em seu menor eixo.

![dataset_sample](http://web.mit.edu/torralba/www/allIndoors.jpg)

A preparação do dataset utilizado para o treino dos modelos pode ser encontrado no notebook `notebooks/indoors_scenes_dataprep.ipynb`. Os experimentos foram nas imagens da classe `bedroom`, que contém 662 imagens no total.

### Modelos Avaliados

Abaixo segue a descrição dos modelos utilizados.

#### FastGAN

Para a arquitetura FastGAN, foi utilizado o [repositório](https://github.com/odegeasslbc/FastGAN-pytorch) disponibilizado no paper ["Towards Faster and Stabilized GAN Training for High-fidelity Few-shot Image Synthesis"](https://arxiv.org/abs/2101.04775). Tal modelo foi escolhido, pois apresenta uma estrutura GAN 'light-weight' capaz de gerar imagens de alta qualidade (resolução 1024 x 1024) com custo computacional reduzido (conseguimos rodar no colab utilizando cerca de 12 horas de treinamento) e com boa performance em datasets pequenos (de acordo com o paper, com menos de 100 amostras de treinamento).

O modelo foi treinado em 3 fases de 50k iterações cada uma, totalizando 150k, onde os checkpoints foram salvos e retomados de acordo com a disponibilidade do colab. Foi utilizado um batch-size de 8 imagens, sendo que cada uma delas foi redimensionada para as dimensões 1024 x 1024 nos canais RGB. O vetor latente utilizado foi de 256. Nesse treinamento, foram utilizadas 331 imagens da classe 'bedroom' para treinamento, enquanto outras 331 imagens foram usadas para validação nas métricas de avaliação.

#### GAN

Implementamos a GAN utilizando o dataset com imagens redimensionadas para as dimensões 28x28 nos canais RGB. Nossa implementação da GAN em PyTorch é uma adaptação da GAN do repositório [building-a-simples-vanilla-gan-with-pytorch](https://github.com/christianversloot/machine-learning-articles/blob/main/building-a-simple-vanilla-gan-with-pytorch.md). Realizamos dois experimentos utilizando uma GAN vanilla previamente validada no dataset [MNIST](https://pytorch.org/vision/main/generated/torchvision.datasets.MNIST.html) com rede geradora de taxonomia 128x256x512 e rede discriminadora de taxonomia 1024x512x256 usando como função de loss entropia binária cruzada. O modelo foi treinado por 50 épocas utilizando o dataset completo (15620 exemplos) com todas as classes.


#### DCGAN

Para a implementação da DCGAN utilizamos o mesmo modelo presente no tutorial [DCGAN Tutorial](https://pytorch.org/tutorials/beginner/dcgan_faces_tutorial.html), uma vez que este foi capaz de gerar estruturas faciais que se assemelhavam a rostos humanos reais, apesar de obterem algumas distorções presentes. As redes geradora e discriminadora possuem, respectivamente, 3.7M e 2.8M parâmetros treináveis.


Com o propósito de fazer uma comparação entre ambas as aplicações, geração de faces humanas e geração de espaços indoor da classe `bedroom`, decidimos utilizar os mesmos hiperparâmetros e aumentar o número de épocas de treino para 1350, para obter um número de iterações próximo ao do tutorial (8000).


#### VAE com camada convolucional

Para a implementação da VAE com camadas convolucionais, utilizamos a implementação presente no projeto [PyTorch VAE](https://github.com/AntixK/PyTorch-VAE), que fornece modelos de diversas classes de VAEs utilizando o framework PyTorch. Para os experimentos realizados, foi utilizada uma arquitetura convolucional tanto no encoder, quanto no decoder, com dimensão do espaço latente de 512, imagens em 256x256, gerando modelos com aproximadamente 54M de parâmetros treináveis.

### Proposta de avaliação

Utilizamos a _nearest neighbor Adversarial Accuracy_ (AA) [8] para calcular a _Privacy Loss_ (AA_test - AA_train) que verifica se o modelo está simplesmente copiando as imagens do conjunto de treino, indo contra as premissas do projeto de criar novas imagens. Uma boa maneira de interpretar os resultados da Adversarial Accuracy é pensar que ela representa o desempenho de um classificador KNN que tenta discriminar o dataset sintético do dataset original. Idealmente, se as amostras sintéticas reproduzem perfeitamente a distribuição original, espera-se que o desempenho desse classificador seja aleatório e, portanto, o valor da Adversarial Accuracy tenderia a 0.5. Portanto, quanto mais distante desse valor ideal, menos realistas são as imagens sintéticas.  

Para calcular a semelhança entre os dados de maneira _pairwise_, este método utiliza uma distância entre data points. Dessa maneira, adaptamos essa distância que utilizava originalmente de dados tabulares como entrada para uma distância entre imagens. A distância sugerida por nosso grupo foi usar a distância de cosseno entre vetores do embedding da ResNet. Também utilizamos a métrica de distância perceptual LPIPS [11].

Utilizamos a Fréchet Inception Distance (FID), introduzida em [9], que transforma amostras sintetizadas num feature vector especificado por uma camada da Inception Net. Analisando este embedding como uma gaussiana multivariada, a média e a covariância são calculadas para os dados sintéticos e para os dados reais. A distância de Fréchet entre essas duas gaussianas (também conhecida como distância de Wasserstein-2) é usada para quantificar a qualidade das amostras sintetizadas. Um menor FID significa uma menor distância entre as distribuições de dados sintéticos e reais. 

Utilizamos as distâncias cosseno e LPIPS para gerar uma lista com as 4 imagens mais próximas à imagem sintética de referência. Dessa maneira, exploramos, qualitativamente, se os elementos da imagem gerada são uma composição das imagens mais parecidas no dataset que foi utilizado para o treinamento.

O processo de avaliação foi realizado apenas na FastGAN. 

## Resultados e Discussão dos Resultados

A FastGAN foi o único modelo que obteve resultados qualitativos passíveis para uma análise objetiva mais crítica, os resultados dos demais modelos encontram-se no [Apêndice A](https://github.com/eyujis/IA376L-Project/blob/main/README.md#ap%C3%AAndice-a).

### Resultados FastGAN

Os resultados da FastGAN foram explorados de maneira mais aprofundada. Foram gerados resultados de Adversarial Accuracy, Privacy Loss e FID, além de uma análise qualitativa das distribuições utilizando PCA, para modelos treinados com batch size de 8 e 10K, 50K, 100K e 150K iterações. Os resultados foram comparados com os gerados pelo dataset de validação, com 331 imagens reais não utilizados no período de treinamento.  

<p align="center"><img alt="" src="https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/table_results.png"></p>

#### Métricas objetivas

Como valores de referência para a FID e AA(Treino), calculamos a métrica entre as 331 imagens dos dados de treino e as 331 imagens dos dados de validação. O resultado pode ser visto na Tabela 1.

Posteriormente, calculamos a FID entre os dados de treino da FastGAN e 331 imagens geradas por 4 diferentes checkpoints (10k, 50k, 100k, 150k) a fim de visualizar a melhora de desempenho conforme o treinamento avançou. A FID nos indica que o checkpoint que alcançou a maior proximidade com a distribuição de treinamento foi o da iteração 50k e que não houve melhora após isso. Além disso, esse valor ainda está distante do FID de referência que buscávamos entre os dados de treino e validação.

Isso também ocorreu para a métrica de Adversarial Accuracy, onde o checkpoint de 50k alcançou melhor desempenho.

O valor da Privacy Loss reflete, de maneira diretamente proporcional, o grau de "vazamento" de informações de treino para as amostras sintéticas. Em todos as iterações testadas, a métrica foi aproximadamente nula, o que indica um baixo grau de cópia das informações usadas durante o aprendizado da rede. Contudo, a baixa qualidade das fotos, demonstradas pelos altos AA e FID, podem contribuir para uma métrica de Privacy Loss baixa, pois a falta de qualidade produz imagens diferentes o suficiente das imagens originais.

#### Análise via PCA

Realizamos uma análise visual dos dados utilizando o PCA, contudo, utilizando duas componentes o método foi capaz de amostrar apenas 12% da variância. Apesar disso, percebe-se que os dados de validação (REAL) tiveram uma dispersão mais semelhante aos dados de treino comparado aos exemplos sintetizados, que tiveram uma maior concentração nas regiões centrais da distribuição real. 

<p align="center">
<img width="280" height="550" alt="screen shot 2017-08-07 at 12 18 15 pm" src="https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/pca_real.png">
</p>

| | |
|:-------------------------:|:-------------------------:|
|<img width="1604" alt="screen shot 2017-08-07 at 12 18 15 pm" src="https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/pca_10k.png"> |  <img width="1604" alt="screen shot 2017-08-07 at 12 18 15 pm" src="https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/pca_50k.png">|
|<img width="1604" alt="screen shot 2017-08-07 at 12 18 15 pm" src="https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/pca_100k.png">  |  <img width="1604" alt="screen shot 2017-08-07 at 12 18 15 pm" src="https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/pca_150k.png">|


#### Comparação das imagens geradas com a base de treino

O grau de semelhança entre a foto gerada e as imagens de treino foi calculado usando tanto a distância perceptual via LPIPS, como a distância cosseno dos embeddings gerados pela ResNet.

Nota-se que, apesar das imagens geradas não serem idênticas as vistas no treino, alguns indícios sugerem que elas são resultantes de uma composição de uma ou mais características das imagens presentes em treino. Um exemplo disso ocorre na primeira foto, onde há a reprodução da janela com paisagem verde e elementos do teto das fotos originais na foto gerada sinteticamente. Essa afirmação ainda é apenas uma hipótese, seriam necessárias mais análises quantitativas entre diferentes imagens para concluir a ocorrência deste fenômeno.

Foram gerados conjuntos de 5 fotos seguindo o seguinte padrão, da esquerda para a direita:

<p align="center"> Foto sintética -> 1ª foto real mais próxima -> 2ª foto real mais próxima -> 3ª foto real mais próxima -> 4ª foto real mais próxima </p>

![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/lpips_19.png)
![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/cosine_19.png)  

![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/lpips_15.png)
![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/cosine_15.png)   

![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/lpips_112.png)
![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/cosine_112.png)   

![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/lpips_331.png)
![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/cosine_331.png)


## Conclusão



## Referências Bibliográficas

[1] [Indoor Scene Dataset](http://web.mit.edu/torralba/www/indoor.html)

[2] [Quattoni, A., & Torralba, A. (2009, June). Recognizing indoor scenes. In 2009 IEEE conference on computer vision and pattern recognition (pp. 413-420). IEEE.](http://people.csail.mit.edu/torralba/publications/indoor.pdf)

[3] [Goodfellow, I., Pouget-Abadie, J., Mirza, M., Xu, B., Warde-Farley, D., Ozair, S., ... & Bengio, Y. (2014). Generative adversarial nets. Advances in neural information processing systems, 27.](https://proceedings.neurips.cc/paper/2014/hash/5ca3e9b122f61f8f06494c97b1afccf3-Abstract.html)

[4] [Kingma, D. P., & Welling, M. (2013). Auto-encoding variational bayes. arXiv preprint arXiv:1312.6114.](https://arxiv.org/abs/1312.6114)

[5] [Sohn, K., Lee, H., & Yan, X. (2015). Learning structured output representation using deep conditional generative models. Advances in neural information processing systems, 28.](https://papers.nips.cc/paper/2015/file/8d55a249e6baa5c06772297520da2051-Paper.pdf)

[6] [Naeem, M. F., Oh, S. J., Uh, Y., Choi, Y., & Yoo, J. (2020, November). Reliable fidelity and diversity metrics for generative models. In International Conference on Machine Learning (pp. 7176-7185). PMLR.](http://proceedings.mlr.press/v119/naeem20a/naeem20a.pdf)

[7] [Borji, A. (2019). Pros and cons of gan evaluation measures. Computer Vision and Image Understanding, 179, 41-65.](https://arxiv.org/pdf/1802.03446.pdf)

[8] [Yale, A., Dash, S., Dutta, R., Guyon, I., Pavao, A., & Bennett, K. P. (2020). Generation and evaluation of privacy preserving synthetic health data. Neurocomputing, 416, 244-255.](https://www.sciencedirect.com/science/article/pii/S0925231220305117)

[9] [Heusel, M., Ramsauer, H., Unterthiner, T., Nessler, B., & Hochreiter, S. (2017). Gans trained by a two time-scale update rule converge to a local nash equilibrium. Advances in neural information processing systems, 30.](https://proceedings.neurips.cc/paper/2017/hash/8a1d694707eb0fefe65871369074926d-Abstract.html)

[10] [Cai, L., Gao, H. and Ji, S., 2019, May. Multi-stage variational auto-encoders for coarse-to-fine image generation. In Proceedings of the 2019 SIAM International Conference on Data Mining (pp. 630-638). Society for Industrial and Applied Mathematics.](https://arxiv.org/abs/1705.07202)

[11] [Zhang, R., Isola, P., Efros, A. A., Shechtman, E., & Wang, O. (2018). The unreasonable effectiveness of deep features as a perceptual metric. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 586-595).](https://openaccess.thecvf.com/content_cvpr_2018/html/Zhang_The_Unreasonable_Effectiveness_CVPR_2018_paper.html)

## Apêndice A

### Resultados da GAN

Para o primeiro experimento no notebook `notebooks/GAN_full_dataset.ipynb` utilizamos o dataset completo com 15620 exemplos e treinamos o modelo para 50 épocas, a imagem abaixo são 16 samples geradas pela rede geradora após a última época. 

![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/GAN_sample_full_dataset_epoch_50.png)

Percebe-se que o modelo não foi capaz de gerar imagens de ambientes indoor. Vale mencionar que realizamos um experimento com o mesmo modelo porém para um dataset de apenas duas imagens, no notebook `notebooks/GAN_overfit_dataset.ipynb`, e o modelo foi capaz de gerar as duas imagens, logo podemos concluir que o modelo não teve a capacidade de generalização para uma maior diversidade de exemplos. Durante este processo, encontramos o repositório [GANs Indoor Scene Recognition](https://github.com/NVukobrat/GANs-Indoor-Scene-Recognition), com uma GAN treinada no mesmo dataset que o do nosso experimento. Os autores tiveram resultados semelhantes aos nossos, a GAN apenas alcança capacidade de replicar imagens de espaços indoor diretamente de exemplos de datasets pequenos, com menos de cinco images de uma mesma classe. Nosso grupo supõe que isso ocorre pelo fato de espaços indoores terem regularidades e padrões menos evidentes quando comparados ao [rosto humano](https://thispersondoesnotexist.com/image) e [números escritos à mão](https://machinelearningmastery.com/how-to-develop-a-generative-adversarial-network-for-an-mnist-handwritten-digits-from-scratch-in-keras/), ambos casos que possuem precedentes de sucesso com GANs. 

Dessa maneira, diferente de nosso planejamento inicial que tinha como próximo passo o desenvolvimento de um Conditional GAN (CGAN), desenvolvemos uma Deep Convolutional GAN (DCGAN) para verificar se a estrutuda profunda de convoluções, propícia para geração de imagens de alta fidelidade, seria capaz de generalizar mais exemplos distintos do dataset.


### Resultados da DCGAN

Devido a alta variabilidade do Indoor Recognition Dataset, decidimos reduzir nossos experimentos a classe `bedroom` para a DCGAN. O notebook da implementação encontra-se em `notebooks/DC_GAN.ipynb`. Durante a execução, percebeu-se que na época 1100 já era possível encontrar imagens geradas que se assemelhavam a quartos, mas com muitas distorções, como demonstrado na figura abaixo: 
![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/DC_GAN_1100.png)

Além disso, as losses do discriminador e do gerador se manteram instáveis durante o treino comparando-se aos resultados apresentados no tutorial [DCGAN Tutorial](https://pytorch.org/tutorials/beginner/dcgan_faces_tutorial.html). A figura abaixo apresenta as losses durante o treinamento do nosso modelo:

![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/DC_GAN_loss.png)

Nota-se que a loss do gerador não converge como ocorre no tutorial, ela segue uma crescente até um decaimento abrupto próximo ao fim do treino. Julgamos pela baixa qualidade dos resultados não prosseguir com uma avaliação mais profunda deste modelo. Principalmente por conta da FastGAN apresentar melhores resultados qualitativos. 

### VAE com camadas convolucionais

#### Experimento 1

Dimensão do espaço latente de 256, imagens em 64x64 e treinamento com 100 épocas, executado em aproximadamente 5h em uma Tesla-P100. A arquitetura completa pode ser encontrada [aqui](https://github.com/heldervj/PyTorch-VAE/blob/master/models/vanilla_vae.py).

Assim como o esperado, obtivemos um resultado com imagens borradas [10], mas o aprendizado da rede ao longo das épocas foi notável.

Samples amostrados espaço latente -> decoder na época 0:  

![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/VanillaVAE-256_Epoch_0.png)

Samples amostrados espaço latente -> decoder na época 49:  

![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/VanillaVAE-256_Epoch_49.png)

Samples amostrados espaço latente -> decoder na época 99:  

![](https://raw.githubusercontent.com/eyujis/IA376L-Project/main/reports/figures/VanillaVAE-256_Epoch_99.png)

Apesar das imagens pouco interpretáveis, é possível observar que a solução gera exemplos dentro da paleta de cores, além de representar primitivamente os formatos encontrados no dataset original. Mesmo assim, ainda testaremos arquiteturas mais complexas, com diferentes tamanhos do espaço latente e mais épocas durante o treinamento.

#### Experimento 2

Dimensão do espaço latente de 512, imagens em 256x256 e treinamento com 1500 épocas, executado em aproximadamente 12h em uma Tesla-P100. A arquitetura completa pode ser encontrada [aqui](https://github.com/heldervj/PyTorch-VAE/blob/master/models/vanilla_vae.py).



