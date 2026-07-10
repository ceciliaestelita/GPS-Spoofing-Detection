# 🛰️ GPS Spoofing Detection

![Status](https://img.shields.io/badge/Status-Completed-brightgreen)
![Platform](https://img.shields.io/badge/Language-Python-blue)
![Model](https://img.shields.io/badge/Model-Random%20Forest-orange)
![Dataset](https://img.shields.io/badge/Dataset-TEXBAT-red)

Este projeto consiste em um sistema inteligente para a detecção de ataques de falsificação de sinais (*spoofing*) em sistemas de navegação por satélite (GPS/GNSS). Desenvolvido como o projeto final da disciplina **ES413 Sinais e Sistemas** na **Universidade Federal de Pernambuco (UFPE)**, o sistema utiliza extração de características espectrais e aprendizado de máquina para diferenciar sinais legítimos de transmissões maliciosas baseadas em *overpowering*.

## ✨ Funcionalidades

* **Extração de Features Espectrais:** Análise de janelas complexas de sinal ($I+jQ$) no domínio da frequência usando a Transformada Rápida de Fourier (FFT).
* **Janelamento Avançado:** Aplicação da janela de Hanning para suavização de bordas e atenuação de vazamento espectral.
* **Métricas SQM (Signal Quality Monitoring):** Extração de indicadores físicos como potência espectral, estimativa de $C/N_0$, centróide espectral (proxy de Doppler), curtose e largura espectral.
* **Validação Cross-Satellite:** Divisão de treino e teste baseada no ID do satélite (PRN), forçando o modelo a generalizar o padrão físico do ataque em vez de memorizar as condições do canal.
* **Classificação Robusta:** Classificador baseado em Random Forest com 100 árvores de decisão para evitar *overfitting*.

## 🛠️ Detalhes Teóricos e Algoritmos

| Componente / Etapa | Descrição e Conceitos de Sinais e Sistemas |
| :--- | :--- |
| **Dataset de Base** | **TEXBAT** (Arquivos *Clean Static* e cenário *DS2 - Static Overpowered Time Push*). |
| **Taxa de Amostragem ($f_s$)** | $25 \text{ MHz}$ ($25$ milhões de amostras complexas por segundo). |
| **Tamanho da Janela** | $32.768$ amostras (aproximadamente $1,31 \text{ ms}$). |
| **Teorema de Parseval** | Utilizado para mapear a potência total do sinal do domínio do tempo para a frequência. |
| **Modelo de ML** | **Random Forest Classifier** (100 estimadores, profundidade máxima de 15). |

## 📊 Principais Características Extraídas (Features)

O modelo avalia a assinatura do sinal a partir da Densidade Espectral de Potência (DEP) gerada com auxílio da Transformada Discreta de Fourier:

* **`power_db`:** Energia *in-band* total do espectro em decibéis.
* **`cn0_dbhz`:** Razão portadora-ruído aproximada a partir do PSD ($\pm1.023 \text{ MHz}$).
* **`centroid_hz`:** Centro de massa do espectro (proxy físico do Efeito Doppler).
* **`spectral_spread`:** Largura espectral que mede a dispersão de energia ou distorção do pico de correlação.
* **`kurtosis`:** Medida de curtose para detectar picos anômalos e agudos provocados pela portadora injetada pelo *spoofer*.

## 📈 Resultados Obtidos

* **Acurácia de Validação:** **100%** utilizando o esquema *Cross-Satellite Validation*.
* **Robustez Realista:** Ao isolar os satélites de teste (PRNs não observados no treino), comprovou-se que o modelo aprendeu as distorções físicas provocadas pelo ataque (como o salto abrupto de $\approx 9 \text{ dB}$ de potência e ganho em $C/N_0$), eliminando falsas métricas causadas por *overfitting*.

> **⚠️ Limitações Identificadas:** A assinatura do cenário DS2 do TEXBAT possui uma disparidade de potência muito acentuada, tornando a classificação trivial neste ambiente controlado. Além disso, a extração de fase do sinal por meio de circuitos simples mostrou-se altamente sensível a ruídos sem o uso de estruturas mais complexas como filtros de Kalman acoplados ao PLL.

## 🚀 Trabalhos Futuros

1. **Cenários Dinâmicos:** Avaliar e treinar o classificador em ambientes onde o receptor e o transmissor espião se movimentam simultaneamente.
2. **Filtro de Kalman no PLL:** Implementar estimadores recursivos para otimizar o rastreamento e reduzir o ruído de fase da portadora.
3. **Hardware & Mitigação Ativa:** Prototipar um sistema embarcado capaz de processar sinais de radiofrequência em tempo real e aplicar contramedidas ou interferências destrutivas automáticas contra o sinal invasor.

---

### 🎓 Autores e Créditos
* **Ana Cecília Estelita Costa** (<acec>)
* **Miriam Rayane Mendes da Silva** (<mrms>)
* **Orientador:** Prof. Divanilson Rodrigo Campelo
* **Instituição:** Centro de Informática (CIn) - Universidade Federal de Pernambuco (UFPE)
