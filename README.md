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

2.  **Reinicie o ambiente: **
    * **Este é o passo mais importante.** Após a instalação terminar, vá ao menu do Colab e clique em:
    * **Ambiente de execução -> Reiniciar ambiente de execução**
    * Isso é necessário para que o `librosa` reconheça o `resampy`.

3.  **Execução da Análise:**
    * Após reiniciar, **não execute a célula de instalação novamente**.
    * O *pipeline* irá montar o Google Drive, baixar os dados, processar os áudios e realizar a análise final (PCA, K-Means).

---

