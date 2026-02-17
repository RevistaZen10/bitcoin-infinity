# Bitcoin Infinity (∞)

> **A Perpetual Continuity Protocol** > Proposta de modificação mínima e matematicamente fundamentada para o Bitcoin Core para garantir a segurança da rede e a estabilidade da oferta a longo prazo.

---

## 📌 Visão Geral

O **Bitcoin Infinity** aborda uma vulnerabilidade crítica de longo prazo no design original do Bitcoin: a contração permanente da oferta causada pela falha na herança de chaves privadas (**Generational Loss Model**) e a eventual extinção do subsídio de bloco (Block Reward).

Este projeto formaliza a transição de um modelo de suprimento fixo para um modelo de **equilíbrio estável**, garantindo que o orçamento de segurança da mineração nunca seja zerado, protegendo a rede contra ataques de 51% perpetuamente.

## ⚠️ O Problema: Vulnerabilidade de Longo Prazo

Segundo o modelo de perda geracional apresentado no paper:
* **Perda de Suprimento:** Estimativas realistas projetam que **64%** do suprimento total se tornará inacessível em ~260 anos.
* **Risco de Ataque:** A interrupção das recompensas no bloco 6.930.000 (aprox. ano 2140) elimina o incentivo financeiro dos mineradores, expondo a rede a riscos sistêmicos.

## 🚀 A Solução: Modificação `GetBlockSubsidy()`

A proposta substitui um condicional de interrupção (*hard-stop*) por uma operação de **módulo**, reiniciando a curva de halving original de 50 BTC a cada 33 halvings (aproximadamente a cada 132 anos).

### Diferenciais Técnicos
- **Equilíbrio Dinâmico:** O suprimento circulante converge para um equilíbrio estável $C^* = S_0r / (1 - r)$.
- **Compatibilidade:** Mantém compatibilidade total com a rede existente até o bloco de ativação.
- **Segurança Verificada:** Implementação testada contra 113 testes de limite (*boundary tests*) com zero falhas.
- **C++17 Standard:** Código limpo, sem comportamentos indefinidos.

## 📊 Modelo Matemático

Sob este esquema, com uma perda geracional de 30%, o suprimento estabiliza em aproximadamente **49M BTC**, garantindo que o Bitcoin continue sendo um ativo escasso, mas funcional como meio de troca e reserva de valor protegida.



## 🛠 Como Implementar (Conceitual)

A alteração no núcleo do Bitcoin Core foca na função de subsídio:

```cpp
// Exemplo conceitual da lógica Bitcoin Infinity
CAmount GetBlockSubsidy(int nHeight, const Consensus::Params& consensusParams)
{
    int halvings = nHeight / consensusParams.nSubsidyHalvingInterval;
    
    // A cada 33 halvings (~132 anos), a curva de emissão reinicia
    halvings %= 33; 

    CAmount nSubsidy = 50 * COIN;
    nSubsidy >>= halvings;
    return nSubsidy;
}
