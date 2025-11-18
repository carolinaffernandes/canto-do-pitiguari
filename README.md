## 🐦 Análise de Variação Clinal no Canto do Pitiguari (*Cyclarhis gujanensis*)

![Python](https://img.shields.io/badge/Python-3.10-3776AB.svg?style=flat&logo=python&logoColor=white)
![Librosa](https://img.shields.io/badge/Librosa-0.10-FF5722.svg?style=flat)
![Scikit-learn](https://img.shields.io/badge/Scikit--learn-1.3-F7931E.svg?style=flat&logo=scikitlearn&logoColor=white)

Este projeto utiliza processamento de sinais e Machine Learning para analisar a variação geográfica no canto do Pitiguari (Rufous-browed Peppershrike).

> O objetivo central foi validar a hipótese de que a variação vocal da espécie **não se manifesta em dialetos discretos**, mas sim como uma **variação clinal** (gradual) correlacionada com a **latitude**.
> Esse mesmo código também pode ser aplicado a outras espécies para verificar características do canto: se apresenta variações contínuas (clinais) ou discretas (dialetos)


Este trabalho foi desenvolvido para a disciplina de Modelos Computacionais aplicados a dados biológicos (BIZ5801) em ambiente Google Colab, com os dados armazenados no Google Drive.

---

## 🎯 Índice

* [Metodologia](#-metodologia)
* [Resultados da Análise](#-resultados-da-análise)

---

## 📈 Metodologia

O *pipeline* de análise foi dividido em quatro fases principais, executadas sequencialmente:

1.  **Fase 1: Coleta de Metadados**
    * Utilizou a API v3 do [Xeno-canto](https://xeno-canto.org/) para buscar 279 gravações (Qualidade A) de *Cyclarhis gujanensis* em toda sua distribuição geográfica.
    * Salvou um arquivo `pitiguari_metadata.csv` contendo `id`, `latitude`, `longitude` e `audio_url`.

2.  **Fase 2: Download dos Áudios**
    * Realizou o download dos 279 arquivos de áudio para uma pasta no Google Drive (`/xenocanto-pitiguari/raw_audio/`).

3.  **Fase 3: Extração de Features (Recorte de 3s)**
    * Esta foi a etapa crítica de limpeza de dados. Para cada áudio, o *pipeline* automaticamente:
        1. Encontrou o ponto de maior energia (pico do canto).
        2. Recortou um clipe padronizado de 3 segundos ao redor desse pico.
    * Isso isolou o "sinal" (o canto) do "ruído" (vento, silêncio), resultando em 273 amostras limpas.
    * Foram extraídas 10 *features* bioacústicas de cada clipe (ex: `f0_mean`, `spectral_centroid_mean`, `wiener_entropy_mean`, etc.).

4.  **Fase 4: Análise e Modelagem**
    * O dataset final limpo (`dados_completos_analise_3sec_clip.csv`) foi usado para testar a hipótese clinal.
    * **PCA** (Análise de Componentes Principais) foi usada para visualização.
    * **K-Means** (com Silhouette Score) foi usado para testar estatisticamente a ausência de dialetos.

---

## 📊 Resultados da Análise

A análise confirmou com sucesso as duas hipóteses centrais da literatura:

### 1. Prova Visual da Variação Clinal (PCA)

A Análise de Componentes Principais (PCA) foi usada para criar um "mapa de similaridade" dos cantos. O gráfico resultante **não mostra "ilhas" ou clusters discretos**, mas sim uma **única nuvem contínua**.

Ao colorir esta nuvem pela latitude, um **gradiente clinal** é claramente visível:
* **Cantos Equatoriais** (cores claras, -10 a 10) agrupam-se no centro do mapa, indicando que são acusticamente similares entre si.
* **Cantos Temperados** (cores escuras, -30 e 20) são "empurrados" para as bordas do mapa, mostrando que são acusticamente diferentes dos equatoriais.
> Ver a imagem no PCA.png no repositório

### 2. Prova Estatística da Ausência de Dialetos (K-Means)

Para provar estatisticamente que não existem "dialetos" (grupos discretos), um modelo K-Means (k=3) foi usado para *tentar forçar* os dados em 3 grupos.

A qualidade desses grupos foi medida com a **Pontuação de Silhueta (Silhouette Score)**.
* Um score perto de +1.0 significaria dialetos reais e bem definidos.
* Um score perto de 0.0 significaria que os grupos são artificiais e se sobrepõem.

**Resultado: 0.3560**

Esta pontuação "Média-Baixa" confirma estatisticamente o que vimos no PCA: os "clusters" são fracos, artificiais e não representam dialetos reais. A variação é, de fato, um gradiente contínuo.

> Ver Kmeans.png no repositório

---

1.  **Configuração Inicial:**
    * Abra o notebook `pitiguari.ipynb` no Google Colab.
    * Na primeira célula do notebook, execute os seguintes comandos para instalar as dependências necessárias:
    ```bash
    !pip install librosa -q
    !pip install resampy -q
    ```

2.  **Reinicie o ambiente:**
    * **Este é o passo mais importante.** Após a instalação terminar, vá ao menu do Colab e clique em:
    * **Ambiente de execução -> Reiniciar ambiente de execução**
    * Isso é necessário para que o `librosa` reconheça o `resampy`.

3.  **Execução da Análise:**
    * Após reiniciar, **não execute a célula de instalação novamente**.
    * O *pipeline* irá montar o Google Drive, baixar os dados, processar os áudios e realizar a análise final (PCA, K-Means).

---

Autores: Bianca, Maria Carolina, Mariana, Raphaela, Ulisses

#### Referências:

>BAKER, M. C.; CUNNINGHAM, M. A. The biology of bird-song dialects. Behavioral and Brain Sciences, v. 8, n. 1, p. 85-100, 1985.

>BOCACCIO, H. et al. Identification of dialects and individuals of globally threatened Yellow Cardinals using neural networks. Ecological Informatics, v. 78, p. 102372, 2023.

>BREWER, D.; BONAN, A.; JUANA, E. Rufous-Browed Peppershrike (Cyclarhis gujanensis). In: HOYO, J. et al. Birds of the World. Ithaca, NY, USA: Cornell Lab of Ornithology. 2020.

>FALLS, J. B. & BROOKS R. J. Individual recognition by song in White-throated Sparrows. II. Effects of location. Can. J. Zool, v. 53, p. 1412–1420. 1975.

>HU, S. et al. Deep learning bird song recognition based on MFF-ScSEnet. Ecological Indicators, v. 154, p. e110844, 2023.

>HUXLEY, J. S. Clines: an auxiliary method in taxonomy. Bijdragen tot de Dierkunde, v. 27, n. 1, p. 491-520, 1939.

>HYMAN, J. Countersinging as a signal of aggression in a territorial songbird. Anim. Behav, v. 65, p. 1179–1185. 2003.

>JÄCKEL, Denise et al. Opportunities and limitations: A comparative analysis of citizen science and expert recordings for bioacoustic research. Plos one, v. 16, n. 6, p. e0253763, 2021.

>JOHNSON, L. S. & SEARCY, W. A. Female attraction to male song in house wrens (Troglodytes aedon). Behaviour, v. 133, n. 5-6, p. 357-366, 1996.

>KUMAR, A. Acoustic communication in birds. Resonance, v. 8, p. 44–55. 2003.

>KUS, B. E. Use of restored riparian habitat by the endangered Least Bell's Vireo (Vireo bellii pusillus). Restor. Ecol, v. 6, p. 75–82. 1998.

>MARLER, P. & TAMURA, M. Song "dialects" in three populations of White-crowned Sparrows. The Condor, v. 64, n. 5, p. 368-377. 1962.

>STOWELL, D. Computational bioacoustics with deep learning: a review and roadmap. PeerJ, v. 10, 10:e13152. 2022.

>SKUTCH, A. F. Life histories of Central American highland birds. The Club, 1967.

>TANG, Y.; LIU, C.; YUAN, X. Recognition of bird species with birdsong records using machine learning methods. Plos one, v. 19, n. 2, p. e0297988, 2024.

>TUBARO, P. L. & SEGURA, E. T. Geographic Ecological and Subspecific Variation in the Song of the Rufous-Browed Peppershrike (Cyclarhis gujanensis). The Condor 1995, v. 97, n. 3, p. 792–803. 1995.

>VELLINGA, W. Xeno-canto - Bird sounds from around the world. Xeno-canto Foundation for Nature Sounds. Occurrence dataset. 2025.

>WANG, D. et al. Machine learning reveals cryptic dialects that explain mate choice in a songbird. Nat Commun, v. 13, p. 1630. 2022.

>YANG, J.; CARSTENS, B. C.; PROVOST, K. L. Machine learning reveals that climate, geography, and cultural drift all predict bird song variation in coastal Zonotrichia leucophrys. Ornithology, v. 141, n. 2, p. ukad062, 2024.
