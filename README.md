# duckteam_akcit_camp
Repositório para alocação dos arquivos entregáveis do hackaton Akcit Camp do grupo Duckteam
# 🎛️ Stage Manager AI — Agente Autônomo de Gestão de Crise em Eventos
> **Projeto submetido ao AKCIT Camp Multicêntrico 2026**

O **Stage Manager AI** é um agente autônomo neuro-simbólico projetado para Diretores de Palco (*Stage Managers*) e Produtores Executivos. Ele recalcula cronogramas de eventos ao vivo em tempo real quando ocorrem atrasos, protegendo atrações âncora (Peso 5) e gerando diretrizes táticas operacionais adaptadas à cultura de cada formato de evento (Hackathons, Summits Corporativos, Conferências, Igrejas e Festivais).

---

## 📑 Sumário Navegável do Repositório
1. [Visão Geral e Justificativa de Engenharia](#1-visão-geral-e-justificativa-de-engenharia)
2. [Diagrama do Pipeline do Agente (Fluxo Ponta a Ponta)](#2-diagrama-do-pipeline-do-agente-fluxo-ponta-a-ponta)
3. [Detalhamento dos Inputs (Entradas)](#3-detalhamento-dos-inputs-entradas)
4. [Detalhamento da Lógica Interna (Motor Determinístico)](#4-detalhamento-da-lógica-interna-motor-determinístico)
5. [Política de Decisão do Agente (Raciocínio Contextual)](#5-política-de-decisão-do-agente-raciocínio-contextual)
6. [Guardrails de Segurança e Resiliência](#6-guardrails-de-segurança-e-resiliência)
7. [Detalhamento dos Outputs (Saídas)](#7-detalhamento-dos-outputs-saídas)

---

# Documentação Técnica do Fluxo do Agente — Stage Manager AI

## 1. Visão Geral e Justificativa de Engenharia
Em eventos ao vivo, um erro aritmético de 5 minutos gera multas contratuais pesadas (*curfew*) ou perda de janela de transmissão. Modelos de Linguagem (LLMs) puros sofrem com alucinações matemáticas ao somar e subtrair múltiplas janelas de horários, mas são imbatíveis na interpretação de regras subjetivas de negócios e contratos.

Para resolver esse dilema, o **Stage Manager AI** utiliza uma **Arquitetura Neuro-Simbólica Híbrida**, alinhada às melhores práticas de engenharia de agentes (Seção 9.4 e 10 do Edital):
1. **Camada Simbólica (Motor Matemático Determinístico):** Calcula o déficit real, filtra atividades elegíveis e estabelece limites físicos intransponíveis (*Guardrails*).
2. **Camada Neural (Agente Autônomo de IA):** Interpreta as regras culturais do evento, pondera prioridades contratuais e gera o plano de ação tático para a equipe humana no palco.

---

## 2. Diagrama do Pipeline do Agente (Fluxo Ponta a Ponta)

O fluxo abaixo representa o ciclo de vida completo de uma requisição de gerenciamento de crise dentro do **Stage Manager AI**:

[Operador / Check-in] 
       │ (Reporta Atraso + Grade Atual + Perfil do Evento)
       ▼
┌──────────────────────────────────────────────────────────┐
│ 1. INPUTS & PARSING DE ESTADO                            │
│ - Atraso Acumulado (min) + Motivo da Ocorrência          │
│ - Matriz de Atividades (Previsto, Mínimo, Peso 1-5)      │
│ - Contexto RAG / Regras Culturais do Nicho               │
└──────────────────────────────────────────────────────────┘
       │
       ▼
┌──────────────────────────────────────────────────────────┐
│ 2. MOTOR DETERMINÍSTICO (Pré-Validação / Function Tool)  │
│ - Filtra status == "pendente"                            │
│ - Calcula Folga Segura (Pesos 1-4) e Folga Total         │
│ - Identifica matematicamente se haverá estouro de Curfew │
└──────────────────────────────────────────────────────────┘
       │ (Injeta Estado Validado + Restrições Matemáticas)
       ▼
┌──────────────────────────────────────────────────────────┐
│ 3. NÚCLEO DO AGENTE DE IA (Raciocínio & Planejamento)    │
│ - Analisa Regras Culturais (ex: "Pitch é intocável")     │
│ - Distribui cortes priorizando Pesos 1 -> 4              │
│ - Formula Diretrizes Táticas de Palco para cada corte    │
└──────────────────────────────────────────────────────────┘
       │ (Retorna Schema JSON Estrito)
       ▼
┌──────────────────────────────────────────────────────────┐
│ 4. GUARDRAILS PÓS-INFERÊNCIA & FALLBACK DE SEGURANÇA     │
│ - Trava física: nova_duracao >= duracao_minima           │
│ - Verificação de soma exata de minutos absorvidos        │
│ - Fallback algorítmico em caso de falha/latência de rede │
└──────────────────────────────────────────────────────────┘
       │
       ▼
[OUTPUT: Nova Grade Recalculada + Plano Tático de Backstage]
---

## 3. Detalhamento dos Inputs (Entradas)

O agente é acionado quando o operador reporta um atraso na linha do tempo. O payload de entrada é estruturado em três camadas:

| Parâmetro | Tipo | Descrição Técnica e Papel no Raciocínio do Agente |
| :--- | :--- | :--- |
| `atraso_acumulado_minutos` | `Integer` | Quantidade exata de minutos de atraso que precisa ser absorvida até o fim do dia. |
| `motivo_do_atraso` | `String` | Contexto situacional da crise (ex: *"Fila no credenciamento e falha no microfone sem fio"*). |
| `perfil_evento` | `String` | Arquétipo do evento (ex: *Hackathon Tech*, *Culto/Conferência de Igreja*, *Summit Corporativo*). |
| `regras_culturais` | `String` | Conhecimento de domínio em linguagem natural que instrui o agente sobre o que é sagrado e quais adaptações logísticas são aceitas naquele nicho. |
| `atividades_pendentes[]` | `Array<Object>` | Lista de blocos ainda não iniciados contendo: `id`, `atividade`, `duracaoPrevista`, `duracaoMinima` (piso físico operacional) e `pesoImportancia` (escala de 1 a 5). |

### Exemplo de Payload de Entrada (Input JSON):
json
{
  "perfil_evento": "Hackathon Tech",
  "regras_culturais": "Prioridade máxima para Hacking e Pitches Finais (Peso 5). Abertura e dinâmicas podem sofrer cortes agressivos. Coffee breaks podem virar grab-and-go.",
  "atraso_acumulado_minutos": 35,
  "motivo_do_atraso": "Atraso na liberação do som do palco e fila no credenciamento",
  "folga_total_disponivel_pendentes": 145,
  "atividades_pendentes": [
    {
      "id": 3,
      "atividade": "Keynote Patrocinador Master",
      "duracaoPrevista": 30,
      "duracaoMinima": 15,
      "folgaMaximaCortavel": 15,
      "pesoImportancia": 4
    },
    {
      "id": 7,
      "atividade": "Coffee Break da Tarde & Sorteio de Brindes",
      "duracaoPrevista": 30,
      "duracaoMinima": 5,
      "folgaMaximaCortavel": 25,
      "pesoImportancia": 1
    },
    {
      "id": 8,
      "atividade": "Apresentação dos Pitches & Banca Avaliadora",
      "duracaoPrevista": 90,
      "duracaoMinima": 80,
      "folgaMaximaCortavel": 10,
      "pesoImportancia": 5
    }
  ]
}

---

## 4. Detalhamento da Lógica Interna (Motor Determinístico)

Antes de enviar os dados para o Modelo de Linguagem (Gemini), o motor determinístico em código executa a **Pré-Validação Matemática** em tempo real:

1. **Filtragem de Estado:** Atividades com status `concluido` ou `em_andamento` são bloqueadas para edição, pois o tempo já decorreu ou está em curso. Apenas itens com `status == "pendente"` entram na matriz de otimização.
2. **Cálculo da Folga Elástica Individual ($F_i$):** Para cada atividade pendente $i$, calcula-se o limite máximo de compressão sem inviabilizar a operação:
   $$F_i = \max(0, \text{Duração Prevista}_i - \text{Duração Mínima}_i)$$
3. **Cálculo da Folga Segura ($FS$):** Soma de todas as folgas das atividades de **Peso 1 a 4**:
   $$FS = \sum_{i \in \{p_i < 5\}} F_i$$
4. **Cálculo da Folga Total ($FT$) e Déficit Inabsorvível ($D$):**
   $$FT = \sum_{\text{pendentes}} F_i \quad \text{e} \quad D = \max(0, \text{Atraso} - FT)$$
   - Se $\text{Atraso} \le FS$: O motor informa ao agente que é matematicamente garantido absorver 100% do atraso sem tocar em nenhum bloco de Peso 5.
   - Se $FS < \text{Atraso} \le FT$: O motor autoriza o agente a retirar exatamente $(\text{Atraso} - FS)$ minutos das atividades de Peso 5.
   - Se $\text{Atraso} > FT$: O motor calcula o estouro inevitável ($D$ minutos) para que o agente emita um alerta antecipado de negociação de *curfew* com o espaço físico.

---

## 5. Política de Decisão do Agente (Raciocínio Contextual)

Com os limites matemáticos estabelecidos, o Agente de IA (`gemini-3-flash-preview`) atua como Diretor de Palco aplicando 4 regras hierárquicas de decisão:

1. **Ordem de Sacrifício Ponderada por Peso:**
   - **Peso 1 e 2 (Descartáveis / Baixo Impacto):** Avisos gerais, sorteios, vídeos institucionais e quebra-gelos são cortados até o seu piso mínimo imediatamente.
   - **Peso 3 (Moderados / Pausas Operacionais):** Intervalos de almoço, networking e coffee breaks sofrem compressão moderada combinada com mudança de formato operacional.
   - **Peso 4 (Importantes / Patrocinadores):** Sofrem redução apenas quando os blocos de peso 1 a 3 já foram esgotados.
   - **Peso 5 (Sagrados / Core do Evento):** Pitches finais, tempo de Hacking, Pregação principal ou Keynotes internacionais permanecem **100% blindados**, exceto em estado de calamidade onde a Folga Segura ($FS$) foi totalmente consumida.
2. **Tradução Cultural em Ação Tática (`acao_tatica`):**
   - O agente lê o campo `regras_culturais` e converte a redução fria de minutos em uma instrução executável:
     - *Em um Hackathon:* Transformar um Coffee Break de 30m em 5m instruindo o staff a servir em modo *grab-and-go* nas bancadas.
     - *Em uma Igreja:* Reduzir 15m do bloco de Louvor instruindo o ministro de música a remover 2 canções do repertório e cortar o interlúdio instrumental.
     - *Em um Summit Corporativo:* Cortar 20m do bloco de Q&A instruindo o mestre de cerimônias a recolher perguntas exclusivamente via QR Code no telão para resposta pós-evento.

---

## 6. Guardrails de Segurança e Resiliência

Para garantir que o agente opere com confiabilidade industrial em ambientes críticos de eventos ao vivo, implementamos **4 camadas de Guardrails**:

1. **Guardrail de Schema Estrito (`responseSchema`):** A chamada à API utiliza *Structured Outputs* forçando o retorno exclusivamente no contrato JSON esperado pela aplicação, eliminando texto conversacional fora de padrão ou chaves inexistentes.
2. **Guardrail Pós-Inferência de Piso Físico (Clamping Matemático):** Ao receber o JSON do agente, o código passa por uma função sanitizadora que impede que qualquer alucinação residual viole a duração mínima:
   $$\text{Corte Seguro}_i = \min(\text{Folga Máxima}_i, \max(0, \text{Corte Sugerido IA}_i))$$
3. **Retry com Exponential Backoff:** Tratamento automatizado de instabilidades momentâneas de rede com novas tentativas espaçadas exponencialmente.
4. **Fallback Determinístico Instantâneo (Offline-First Resilience):** Caso o Wi-Fi do centro de convenções caia ou a API externa sofra *timeout*, o motor de contingência local assume a operação de forma transparente em `< 50ms`, distribuindo os cortes matematicamente por ordem crescente de peso (1 a 5) e aplicando templates táticos pré-compilados.

---

## 7. Detalhamento dos Outputs (Saídas)

O agente devolve um objeto JSON validado que atualiza instantaneamente a tabela de horários e renderiza os cartões de comando para a produção:

| Campo de Saída | Tipo | Descrição |
| :--- | :--- | :--- |
| `resumo_estrategia` | `String` | Síntese executiva explicando ao Coordenador Geral como o atraso foi absorvido e quais blocos críticos foram protegidos. |
| `alerta_estouro` | `String` | Vazio se 100% do atraso foi absorvido; caso contrário, informa o minuto exato em que o evento passará do horário limite. |
| `ajustes[].id` | `Integer` | ID correspondente à atividade na matriz do cronograma. |
| `ajustes[].nova_duracao_min` | `Integer` | Nova duração validada da atividade após o corte. |
| `ajustes[].corte_min` | `Integer` | Quantidade exata de minutos deduzidos da atividade original. |
| `ajustes[].acao_tatica` | `String` | Diretriz prática de palco para execução imediata pela equipe de som, luz, apresentadores ou alimentação. |

### Exemplo de Payload de Saída (Output JSON):
json
{
  "resumo_estrategia": "Absorvemos 100% dos 35 minutos de atraso convertendo o Coffee Break da tarde em serviço contínuo (-25 min) e enxugando a dinâmica inicial (-10 min), mantendo intactos os blocos de Hacking e Apresentação de Pitches (Peso 5).",
  "alerta_estouro": "",
  "ajustes": [
    {
      "id": 4,
      "nova_duracao_min": 35,
      "corte_min": 10,
      "acao_tatica": "Sinalizar ao facilitador no palco que a dinâmica de formação de equipes terá 35 min (redução de 10 min) com timer regressivo no telão."
    },
    {
      "id": 7,
      "nova_duracao_min": 5,
      "corte_min": 25,
      "acao_tatica": "Acionar equipe de catering: converter pausa de 30 min em formato Grab-and-Go (retirada rápida na bancada sem interromper o código) e realizar sorteio via aplicativo."
    },
    {
      "id": 8,
      "nova_duracao_min": 90,
      "corte_min": 0,
      "acao_tatica": "Manter duração original intacta (bloco prioritário Peso 5 protegido)."
    }
  ]
}

### Como Executar o Protótipo Localmente

bash
# 1. Clone este repositório
git clone https://github.com/SEU-USUARIO/stage-manager-ai.git

# 2. Acesse a pasta do projeto e instale as dependências
cd stage-manager-ai
npm install

# 3. Inicie o servidor de desenvolvimento
npm run dev
