# HydroCheck — Guia de Uso

Sistema digital de monitoramento de qualidade da água em reservatórios, desenvolvido no simulador WiredPanda como projeto da disciplina de Circuitos Digitais (UNIFESP-ICT).

---

## Requisitos

- **WiredPanda versão 5.1.2** (recomendada para evitar incompatibilidades)
  - Repositório oficial: *(https://github.com/GIBIS-UNIFESP/wiredpanda)*
- **Git** para clonar o projeto

---

## Instalação

```bash
git clone https://github.com/brenoprato/hydro_check
```

Após clonar, abra o WiredPanda e carregue o arquivo:

```
1hydro_check.panda
```

Este é o **circuito principal**, contendo todos os sensores, LEDs de alerta, LEDs de inspeção por sensor e os displays numéricos.

---

## Visão Geral do Circuito

O HydroCheck monitora **5 parâmetros** de um reservatório de água:

| Sensor | Parâmetro | Bits | Faixa | Resolução |
|--------|-----------|------|-------|-----------|
| S1 | Turbidez | 4 bits | 0 – 15 NTU | 1 NTU por bit |
| S2 | pH | 4 bits | 0 – 14 | 1 unidade por bit |
| S3 | Temperatura | 5 bits | 0 – 31 °C | 1 °C por bit |
| S4 | Cloro residual | 4 bits | 0,0 – 3,0 mg/L | 0,2 mg/L por bit |
| S5 | Volume/Nível | 4 bits | 0 – 100% | ~6,67% por bit (100/15) |

> **Tampa (S6):** sensor de 1 bit. `0` = fechada, `1` = aberta.

---

## Como Simular

1. Abra `1hydro_check.panda` no WiredPanda 5.1.2.
2. Localize as entradas de cada sensor no circuito.
3. Defina os valores desejados em binário para cada sensor (veja a tabela de conversão abaixo).
4. Observe os LEDs e displays para interpretar o resultado.

### Tabela de Conversão dos Sensores

#### pH (4 bits — 1 unidade por bit)

| Valor decimal | Binário | pH |
|:---:|:---:|:---:|
| 0 | `0000` | 0 |
| 6 | `0110` | 6 |
| 7 | `0111` | 7 |
| 9 | `1001` | 9 |
| 14 | `1110` | 14 |

#### Turbidez (4 bits — 1 NTU por bit)

| Valor decimal | Binário | Turbidez |
|:---:|:---:|:---:|
| 0 | `0000` | 0 NTU |
| 1 | `0001` | 1 NTU |
| 5 | `0101` | 5 NTU |
| 10 | `1010` | 10 NTU |
| 15 | `1111` | 15 NTU |

#### Cloro residual (4 bits — 0,2 mg/L por bit)

| Valor decimal | Binário | Cloro |
|:---:|:---:|:---:|
| 0 | `0000` | 0,0 mg/L |
| 1 | `0001` | 0,2 mg/L |
| 10 | `1010` | 2,0 mg/L |
| 14 | `1110` | 2,8 mg/L |
| 15 | `1111` | 3,0 mg/L |

#### Volume/Nível (4 bits — 100/15 ≈ 6,67% por bit)

| Valor decimal | Binário | Volume aproximado |
|:---:|:---:|:---:|
| 0 | `0000` | 0% |
| 1 | `0001` | ~6,7% |
| 2 | `0010` | ~13,3% |
| 4 | `0100` | ~26,7% |
| 15 | `1111` | 100% |

#### Temperatura (5 bits — 1 °C por bit)

| Valor decimal | Binário | Temperatura |
|:---:|:---:|:---:|
| 0 | `00000` | 0 °C |
| 24 | `11000` | 24 °C |
| 25 | `11001` | 25 °C |
| 31 | `11111` | 31 °C |

---

## Interpretando as Saídas

### LEDs de Inspeção por Sensor

Use o **botão de navegação** para selecionar qual sensor inspecionar (o display indica o número do sensor: 1 = pH, 2 = volume, 3 = turbidez, 4 = cloro). Três LEDs mostram a zona do sensor selecionado:

| LED | Significado |
|-----|-------------|
| 🟢 **Verde** | Parâmetro dentro dos padrões regulatórios |
| ⚪ **Branco** | Parâmetro em zona aceitável, mas não ideal |
| 🔴 **Vermelho** | Parâmetro fora dos limites — requer atenção |

### Zonas por Sensor

#### pH
| Zona | Condição |
|------|----------|
| 🟢 Verde | 7 ≤ pH ≤ 9 |
| ⚪ Branco | 6 ≤ pH < 7 |
| 🔴 Vermelho | pH < 6 ou pH > 9 |

#### Turbidez
| Zona | Condição |
|------|----------|
| 🟢 Verde | ≤ 1 NTU |
| ⚪ Branco | 1 < turbidez ≤ 5 NTU |
| 🔴 Vermelho | > 5 NTU |

#### Cloro residual
| Zona | Condição |
|------|----------|
| 🟢 Verde | 0,2 ≤ cloro ≤ 2,0 mg/L |
| ⚪ Branco | 2,0 < cloro < 3,0 mg/L |
| 🔴 Vermelho | < 0,2 mg/L ou ≥ 3,0 mg/L |

#### Volume/Nível
| Zona | Condição |
|------|----------|
| 🟢 Verde | ≥ 27% |
| ⚪ Branco | 13,5% ≤ volume < 27% |
| 🔴 Vermelho | < 13,5% |

#### Temperatura
> A temperatura não aparece no sistema de zonas do MUX. Ela contribui apenas para o **Alerta Bacteriano** (ver abaixo) quando ultrapassa 24 °C. O valor é exibido diretamente no **Display de Temperatura** em graus Celsius.

---

### Displays Numéricos

| Display | O que mostra |
|---------|-------------|
| **Display de Temperatura** | Temperatura atual em °C (dois dígitos decimais) |
| **Display do MUX** | Número do sensor sendo inspecionado (1 a 4) |
| **Display de Críticos** | Quantidade de sensores simultaneamente em zona vermelha (0 a 4) |

---

### LEDs de Alertas Combinados

Quando múltiplos parâmetros interagem, o sistema acende alertas específicos. **Nenhum LED aceso = água em condições normais.**

| LED de Alerta | Condição de ativação |
|---------------|---------------------|
| **pH Inseguro** | pH em zona vermelha (< 6 ou > 9) |
| **Volume Crítico** | Volume < 13,5% |
| **Desinfecção Crítica** | Cloro < 0,2 mg/L ou ≥ 3,0 mg/L |
| **Desinfecção Ineficaz** | Turbidez > 5 NTU **e** cloro dentro da faixa (não crítico) |
| **Alerta Bacteriano** | Temperatura > 24 °C **e** cloro em zona vermelha |
| **Contaminação Externa** | Tampa aberta por tempo prolongado **e** turbidez ou cloro críticos |
| **Tampa Aberta** | Tampa aberta por tempo prolongado (detectada pelo flip-flop D) |

> Múltiplos alertas podem estar ativos ao mesmo tempo. Cada um indica um tipo específico de risco, permitindo intervenção direcionada.

---

## Exemplo de Simulação

**Cenário:** água com turbidez alta e cloro adequado.

| Sensor | Valor | Binário |
|--------|-------|---------|
| pH | 7 | `0111` |
| Volume | ~53% (8 × 6,67%) | `1000` |
| Turbidez | 8 NTU | `1000` |
| Cloro | 1,0 mg/L | `0101` |
| Temperatura | 22 °C | `10110` |
| Tampa | Fechada | `0` |

**Resultado esperado:**
- Display de Críticos: **1** (só turbidez em vermelho)
- LED **Vermelho** aceso ao inspecionar sensor 3 (turbidez)
- Alerta **Desinfecção Ineficaz** aceso (turbidez crítica com cloro dentro da faixa)
- Demais sensores em zona verde

---

## Documentação Técnica

Para detalhes sobre a arquitetura do circuito, limiares regulatórios, equações lógicas e referências normativas (Portaria ANVISA 888/2021 e diretrizes da OMS), consulte o arquivo **`HydroCheck.pdf`** disponível no repositório.
