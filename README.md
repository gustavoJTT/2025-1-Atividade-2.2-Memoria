# S.O. 2025.1 - Atividade 2.02 - Gestão de memória

## Informações gerais

- **Objetivo do repositório**: Repositório para atividade avaliativa dos alunos
- **Assunto**: Gestão de memória
- **Público alvo**: alunos da disciplina de SO (Sistemas Operacionais) do curso de TADS (Superior em Tecnologia em Análise e Desenvolvimento de Sistemas) no CNAT-IFRN (Instituto Federal de Educação, Ciência e Tecnologia do Rio Grande do Norte - Campus Natal-Central).
- disciplina: **SO** [Sistemas Operacionais](https://github.com/sistemas-operacionais/)
- professor: [Leonardo A. Minora](https://github.com/leonardo-minora)
- Repositório do aluno: [gustavoJTT/2025-1-Atividade-2.2-Memoria](https://github.com/gustavoJTT/2025-1-Atividade-2.2-Memoria)

## Tarefas do aluno
1. Fork desse repositório e atualizar a linha 10 com o nome e link do github
2. Ler a descrição da atividade
3. Montar a resposta no final deste arqivo, no tópico **Resposta**

---

## 1. Descrição da atividade
### 1.1. Objetivo
Praticar os conceitos de alocação de memória (best-fit), memória virtual e desfragmentação em um sistema com memória limitada.

---

### 1.2. Contexto
Um computador possui apenas **64 KB de RAM** e um **disco rígido para memória virtual**. O sistema operacional deve gerenciar 5 processos com tamanhos diferentes, cuja soma ultrapassa a capacidade da RAM.

#### 1.2.1. Processos iniciais

| Processo | Tamanho (KB) |
|----------|-------------|
| P1       | 20          |
| P2       | 15          |
| P3       | 25          |
| P4       | 10          |
| P5       | 18          |
| **Total**| **88 KB**   |

- **Memória RAM**: 64 KB (contígua, inicialmente vazia).
- **Memória Virtual (Disco)**: Espaço ilimitado para paginação.

#### 1.2.2. Alocação Inicial com Best-Fit
Os alunos devem simular a alocação dos processos na RAM usando o algoritmo **best-fit**.
- A memória RAM será representada como um bloco contíguo (ex: `[0KB - 64KB]`).
- Devem alocar os processos nos menores espaços livres que atendam ao seu tamanho.

**Alocação inicial**:
1. P1 (20 KB) → Ocupa [0-20].
2. P2 (15 KB) → Ocupa [20-15].
3. _continuar a partir daqui_

#### 1.2.3. Simular Memória Virtual (Paginação)
- Os processos não alocados na RAM devem ser "paginados" no disco.
- Criar uma tabela de páginas indicando quais partes estão na RAM e quais estão no disco.

#### 1.2.4. Desfragmentação da RAM
- Desfragmentar a RAM para liberar espaço contíguo.
- Após desfragmentação (compactação), verificar quais processos podem ser alocado.

### 1.3. Questões para Reflexão
1. Best-fit foi mais eficiente que first-fit ou worst-fit neste cenário?
2. Como a memória virtual evitou um deadlock?
3. Qual o impacto da desfragmentação no desempenho do sistema?

---

## Resposta

### 1. Implementação do Algoritmo Best-Fit

**Especificações do Sistema:**
- **Memória Principal:** 64 KB (espaço contíguo)
- **Armazenamento Secundário:** Capacidade ilimitada para swap
- **Conjunto de Processos:**

| ID | Tamanho | Status |
|----|---------|--------|
| P1 | 20 KB   | Aguardando |
| P2 | 15 KB   | Aguardando |
| P3 | 25 KB   | Aguardando |
| P4 | 10 KB   | Aguardando |
| P5 | 18 KB   | Aguardando |
| **Σ** | **88 KB** | **Total** |

---

**Simulação da Estratégia Best-Fit:**

**Etapa 1 - Processo P1 (20 KB):**
- Busca: Encontrado bloco inicial `[0→64]` com 64 KB
- Resultado: Alocado em `[0→20]`
- Remanescente: `[20→64]` = 44 KB disponíveis

**Etapa 2 - Processo P2 (15 KB):**
- Busca: Localizado bloco `[20→64]` com 44 KB
- Resultado: Alocado em `[20→35]`
- Remanescente: `[35→64]` = 29 KB disponíveis

**Etapa 3 - Processo P3 (25 KB):**
- Busca: Identificado bloco `[35→64]` com 29 KB
- Resultado: Alocado em `[35→60]`
- Remanescente: `[60→64]` = 4 KB disponíveis

**Etapa 4 - Processo P4 (10 KB):**
- Busca: Insuficiência de espaço (4 KB < 10 KB necessários)
- Resultado: **Redirecionado para Memória Virtual**

**Etapa 5 - Processo P5 (18 KB):**
- Busca: Insuficiência de espaço (4 KB < 18 KB necessários)
- Resultado: **Redirecionado para Memória Virtual**

**Mapeamento Final dos Recursos:**

*Memória Principal (64 KB):*
```text
║ 0→20  ║ P1 (20 KB) ║
║ 20→35 ║ P2 (15 KB) ║
║ 35→60 ║ P3 (25 KB) ║
║ 60→64 ║ VAZIO (4KB)║
```

*Memória Virtual (Disco):*
```text
➤ P4: 10 KB
➤ P5: 18 KB
```

### 2. Gerenciamento de Memória Virtual através de Paginação

O sistema implementa **paginação dinâmica** para gerenciar processos que excedem a capacidade da memória física, transferindo-os para o dispositivo de armazenamento secundário.

**Parâmetros do Sistema de Paginação:**

- **Capacidade Total da RAM:** 64 KB
- **Unidade de Paginação:** 4 KB por frame
- **Distribuição por Processo:**

| Processo | Tamanho Total | Frames Necessários | Observação |
|----------|---------------|-------------------|------------|
| P1       | 20 KB        | 5 frames          | Totalmente residente |
| P2       | 15 KB        | 4 frames          | Totalmente residente |
| P3       | 25 KB        | 7 frames          | Totalmente residente |
| P4       | 10 KB        | 3 frames          | Paginado no disco |
| P5       | 18 KB        | 5 frames          | Paginado no disco |

**Tabela de Mapeamento de Frames:**

| Frame ID | Proprietário | Status | Localização Física |
|----------|-------------|--------|--------------------|
| 0        | P1          | Ativo  | RAM                |
| 1        | P1          | Ativo  | RAM                |
| 2        | P1          | Ativo  | RAM                |
| 3        | P1          | Ativo  | RAM                |
| 4        | P1          | Ativo  | RAM                |
| 5        | P2          | Ativo  | RAM                |
| 6        | P2          | Ativo  | RAM                |
| 7        | P2          | Ativo  | RAM                |
| 8        | P2          | Ativo  | RAM                |
| 9        | P3          | Ativo  | RAM                |
| 10       | P3          | Ativo  | RAM                |
| 11       | P3          | Ativo  | RAM                |
| 12       | P3          | Ativo  | RAM                |
| 13       | P3          | Ativo  | RAM                |
| 14       | P3          | Ativo  | RAM                |
| 15       | P3          | Ativo  | RAM                |
| 16       | P4          | Swap   | DISCO              |
| 17       | P4          | Swap   | DISCO              |
| 18       | P4          | Swap   | DISCO              |
| 19       | P5          | Swap   | DISCO              |
| 20       | P5          | Swap   | DISCO              |
| 21       | P5          | Swap   | DISCO              |
| 22       | P5          | Swap   | DISCO              |
| 23       | P5          | Swap   | DISCO              |

### 3. Otimização através de Compactação de Memória

**Análise Inicial da Fragmentação:**

Estado atual da memória principal:

```text
╔═══════════════════════════════════════╗
║ Segmento  │ Conteúdo │ Tamanho       ║
╠═══════════════════════════════════════╣
║ 0→20 KB   │    P1    │ 20 KB ocupado ║
║ 20→35 KB  │    P2    │ 15 KB ocupado ║
║ 35→60 KB  │    P3    │ 25 KB ocupado ║
║ 60→64 KB  │  LIVRE   │ 4 KB vazio    ║
╚═══════════════════════════════════════╝
```

- **Espaço total disponível:** 4 KB
- **Maior bloco contíguo:** 4 KB

**Processo de Compactação:**

Como todos os processos já estão organizados sequencialmente sem intervalos, **não há fragmentação interna** a ser corrigida. A memória encontra-se em estado ótimo.

**Resultado pós-compactação:**

```text
╔═══════════════════════════════════════╗
║ Status: INALTERADO - Já otimizada     ║
║ 0→20 KB   │    P1    │ 20 KB ocupado ║
║ 20→35 KB  │    P2    │ 15 KB ocupado ║
║ 35→60 KB  │    P3    │ 25 KB ocupado ║
║ 60→64 KB  │  LIVRE   │ 4 KB vazio    ║
╚═══════════════════════════════════════╝
```

**Cenário Alternativo: Liberação de P2**

Simulando a finalização do processo P2:

**Estado após remoção:**
```text
╔═══════════════════════════════════════╗
║ 0→20 KB   │    P1    │ 20 KB ocupado ║
║ 20→35 KB  │  LIVRE   │ 15 KB vazio   ║
║ 35→60 KB  │    P3    │ 25 KB ocupado ║
║ 60→64 KB  │  LIVRE   │ 4 KB vazio    ║
╚═══════════════════════════════════════╝
```

**Após compactação e realocação:**
```text
╔═══════════════════════════════════════╗
║ 0→20 KB   │    P1    │ 20 KB ocupado ║
║ 20→45 KB  │    P3    │ 25 KB ocupado ║
║ 45→63 KB  │    P5    │ 18 KB ocupado ║
║ 63→64 KB  │  LIVRE   │ 1 KB vazio    ║
╚═══════════════════════════════════════╝
```

**Resultado:** Com 19 KB contíguos liberados, foi possível realocar P5 da memória virtual para a RAM.

**Estado Final dos Recursos:**

_Memória Principal:_
```text
► P1: 20 KB (residente)
► P3: 25 KB (residente)
► P5: 18 KB (migrado do disco)
► Livre: 1 KB
```

_Memória Virtual:_
```text
► P4: 10 KB (permanece no disco)
```

**Mapeamento de Frames Atualizado:**

| Faixa de Frames | Processo | Nova Localização |
|------------------|----------|------------------|
| 0→4              | P1       | RAM (inalterado) |
| 5→11             | P3       | RAM (realocado)  |
| 12→16            | P5       | RAM (do disco)   |
| 17→19            | P4       | DISCO            |

### 4. Análise Crítica e Considerações Técnicas

**Questão 1: Eficiência Comparativa do Algoritmo Best-Fit**

Na simulação executada, o algoritmo best-fit demonstrou performance satisfatória, embora suas vantagens distintivas não tenham sido completamente evidenciadas devido à natureza sequencial da alocação. O método best-fit opera buscando sistematicamente o menor segmento de memória capaz de acomodar cada processo, objetivando minimizar o desperdício de recursos.

Entretanto, neste cenário específico, onde os processos foram inseridos de forma ordenada e encontraram blocos contíguos adequados, tanto o algoritmo first-fit quanto o best-fit produziram resultados equivalentes. O algoritmo worst-fit, por sua característica de selecionar consistentemente os maiores blocos disponíveis, potencialmente resultaria em maior fragmentação, criando resíduos de memória que, embora grandes, poderiam ser inadequados para processos futuros.

**Questão 2: Papel da Memória Virtual na Prevenção de Deadlocks**

O mecanismo de memória virtual desempenhou função crítica ao possibilitar que processos excedentes à capacidade física da RAM fossem transferidos para armazenamento secundário via paginação, prevenindo efetivamente situações de deadlock por escassez de recursos.

Na ausência deste mecanismo, o sistema enfrentaria bloqueios críticos ou seria forçado a rejeitar processos fundamentais, uma vez que todos demandam espaço na memória principal para execução. Através da implementação de swap para disco, o sistema operacional manteve operacionalidade normal mesmo com sobrecarga da RAM, assegurando que nenhum processo fosse descartado ou bloqueado indefinidamente por limitações de espaço físico.

**Questão 3: Implicações da Desfragmentação no Desempenho Sistêmico**

O processo de desfragmentação exerce influência significativa e multifacetada sobre o desempenho computacional. Positivamente, otimiza a eficiência de alocação de memória através da consolidação de espaços livres fragmentados, viabilizando o carregamento de novos processos sem necessidade de recorrer à memória virtual, resultando em redução de latência de acesso e incremento de performance geral.

Contudo, a operação de desfragmentação implica custos computacionais substanciais, demandando movimentação extensiva de dados na RAM, consumo intensivo de ciclos de CPU e interrupção temporária da execução de processos ativos. Consequentemente, sua implementação requer avaliação criteriosa: embora benéfica em cenários com fragmentação significativa que impeça novas alocações, os custos operacionais devem ser ponderados, particularmente em sistemas com alta densidade de processos ou requisitos de tempo real rigorosos.
