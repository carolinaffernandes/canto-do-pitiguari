## 🐦 Análise de Variação Clinal no Canto do Pitiguari (*Cyclarhis gujanensis*)

![Python](https://img.shields.io/badge/Python-3.10-3776AB.svg?style=flat&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-2.0-F0E040.svg?style=flat&logo=pandas&logoColor=black)
![Librosa](https://img.shields.io/badge/Librosa-0.10-FF5722.svg?style=flat)
![Scikit-learn](https://img.shields.io/badge/Scikit--learn-1.3-F7931E.svg?style=flat&logo=scikitlearn&logoColor=white)

Este projeto utiliza processamento de sinais e Machine Learning para analisar a variação geográfica no canto do Pitiguari (Rufous-browed Peppershrike).

> O objetivo central é validar a hipótese de que a variação vocal da espécie **não se manifesta em dialetos discretos**, mas sim como uma **variação clinal** (gradual) fortemente correlacionada com a **latitude**.

Este trabalho é desenvolvido em ambiente Google Colab, com os dados armazenados no Google Drive.

---

## Índice

* [Metodologia](#-metodologia)
* [Resultados Esperados](#-resultados-esperados)
* [Como Executar o Projeto](#-como-executar-o-projeto)
* [Estrutura do Repositório](#-estrutura-do-repositório)

---

## 📈 Metodologia

O *pipeline* de análise é dividido em quatro fases principais, executadas sequencialmente:

1.  **Fase 1: Coleta de Metadados**
    * Utiliza a API v3 do [Xeno-canto](https://xeno-canto.org/) para buscar gravações de *Cyclarhis gujanensis* (Qualidade A) em toda sua distribuição geográfica (Américas Central e do Sul).
    * Salva um arquivo `pitiguari_metadata.csv` contendo `id`, `latitude`, `longitude` e `audio_url` de cada gravação válida.

2.  **Fase 2: Download dos Áudios**
    * Lê o `pitiguari_metadata.csv`.
    * Realiza o download dos arquivos de áudio (ex: `.mp3`) para uma pasta no Google Drive (`/xenocanto-pitiguari/raw_audio/`).
    * Otimizado para pular arquivos que já existem.

3.  **Fase 3: Extração de Features**
    * Este é o núcleo da análise bioacústica.
    * Cada áudio é carregado com `librosa` e um vetor de características (features) é extraído.
    * **Features Avançadas (baseado na literatura):**
        * `duration`: Duração do canto.
        * `f0_mean` / `f0_std`: Média e desvio padrão da frequência fundamental (pitch).
        * `f0_slope`: Inclinação da melodia (se o canto sobe ou desce).
        * `trill_rate_hz`: Taxa de repetição de notas (notas/segundo).
        * `spectral_centroid_mean`: Centro de massa espectral (brilho do som).
        * `spectral_bandwidth_mean`: Largura de banda média (quão "espalhado" é o som).
        * `wiener_entropy_mean`: Medida de "tonalidade vs. ruído" (entropia).
        * `hnr_mean_db`: Relação Harmônicos-Ruído (pureza do tom).

4.  **Fase 4: Análise e Modelagem**
    * Os metadados (GPS) são combinados com as *features* (áudio) para criar o dataset final: `dados_completos_analise_avancado.csv`.
    * **Modelos a serem aplicados:**
        * **Regressão** (`RandomForestRegressor`): Para prever a `latitude` usando apenas as features do canto.
        * **PCA** (Análise de Componentes Principais): Para visualizar a estrutura dos dados e confirmar a ausência de "dialetos" discretos.

---

## 📊 Resultados Esperados

A análise deve replicar os achados da literatura. Especificamente, o gráfico de PCA (Componente Principal 1 vs. Componente Principal 2) não deve mostrar grupos (clusters) distintos, mas sim uma nuvem de pontos contínua.


## Referências:


