# auditor-payroll
# 🧾 Auditor de Folha de Pagamento — Sistema Prompt v3.2

> **System prompt de produção** para auditoria automatizada de folhas de pagamento CLT, pró-labore e bolsa-auxílio (estagiários) com fundamentação legal atualizada para 2026.

[![Status](https://img.shields.io/badge/status-produção-green)]()
[![Versão](https://img.shields.io/badge/versão-3.2-blue)]()
[![Base Legal](https://img.shields.io/badge/base%20legal-2026-orange)]()
[![Jurisdição](https://img.shields.io/badge/jurisdição-Brasil-yellow)]()

---

## 🎯 O Problema

Escritórios de contabilidade processam centenas de folhas de pagamento mensalmente. Cada folha pode ter 20–80 rubricas, cada rubrica com uma regra própria de cálculo — algumas na CLT, outras na CCT do sindicato, outras em portarias que mudam todo janeiro. Um único erro de INSS mal calculado ou IRRF sem a nova redução (Lei 15.270/2025) gera passivo trabalhista, multa e retrabalho.

Auditores humanos são lentos, caros e inconsistentes entre si. A solução óbvia é um LLM — mas LLMs genéricos alucinam legislação, confundem regime CLT com pró-labore e não sabem que o teto do INSS mudou em janeiro.

---

## 💡 A Solução

Um system prompt de **~8.000 tokens** que transforma Claude em um auditor sênior especializado, com:

- Tabelas legais 2026 embutidas e verificáveis
- Lógica de cálculo explícita em pseudocódigo Python
- 7 exemplos few-shot cobrindo os erros mais comuns em produção
- Estratégia de otimização agressiva de tokens para folhas de 40+ rubricas
- Regras inegociáveis que nunca sacrificam qualidade por brevidade

---

## 🏗️ Arquitetura do Prompt

```
system_prompt/
├── [META_INFO]                    # Versão, jurisdição, base legal
├── [SYSTEM_ROLE]                  # Persona + tom + competências
├── [PRIMARY_OBJECTIVE]            # Entregáveis obrigatórios
├── [OPERATIONAL_CONSTRAINTS]
│   ├── <hierarquia_normativa>     # CF → CLT → CCT → ACT → Contrato
│   ├── <tabelas_oficiais_2026>    # INSS, IRRF, Salário Família, FGTS
│   ├── <tabelas_prolabore_2026>   # INSS 11%, Patronal 20%, RAT/FAP
│   ├── <tabelas_estagiarios_2026> # Lei 11.788/2008, recesso, jornada
│   ├── <calculo_inss_detalhado>   # Pseudocódigo progressivo por faixas
│   ├── <calculo_irrf_detalhado>   # Com redução Lei 15.270/2025
│   ├── <premissas_padrao>         # Jornada 220h, HE 50%, AN 20%...
│   └── <tolerancias>              # ≤0,05 OK | ≤1,00 ATENÇÃO | >1,00 ERRO
├── [TOKEN_OPTIMIZATION_AGGRESSIVE]
│   ├── <filosofia_core>           # "Processar tudo, reportar o essencial"
│   ├── <processamento_cct>        # Resumo compacto de CCTs em PDF
│   ├── <processamento_folha>      # Formato adaptável por tamanho
│   ├── <cache_calculos>           # [CALC-BASE] para reutilização
│   └── <regras_inegociaveis>      # O que NUNCA pode ser comprimido
├── [ALGORITHMIC_PROCESS]
│   ├── etapa_0: Identificação CLT/Pró-labore/Estagiário
│   ├── etapa_1: Extração de dados do holerite
│   ├── etapa_2: Análise da CCT (se CLT)
│   ├── etapa_3: Auditoria rubrica a rubrica
│   └── etapa_4: Consolidação e classificação de risco
├── [FEW_SHOT_EXAMPLES]            # 7 exemplos com ENTRADA → PROCESSAMENTO → SAÍDA
├── [OUTPUT_FORMAT_TEMPLATE]       # Templates por tipo: CLT / Pró-labore / Estagiário
├── [ALERTAS_CRITICOS_2026]        # 36 alertas distribuídos por regime
├── [REFERENCIAS_LEGAIS]           # Todas as normas citadas com artigos
├── [GLOSSARIO_PROLABORE]          # Diferença pró-labore vs distribuição de lucros
└── [GLOSSARIO_ESTAGIARIOS]        # Estágio obrigatório vs não obrigatório
```

---

## ⚖️ Cobertura Legal

### Empregados CLT
| Rubrica | Base Legal |
|---------|-----------|
| INSS progressivo | Portaria MPS/MF nº 13/2026, Anexo II |
| IRRF com redução | Lei 15.191/2025 + Lei 15.270/2025 |
| Salário mínimo | Decreto 12.797/2025 — R$ 1.621,00 |
| Salário Família | Portaria MPS/MF 13/2026, Art. 4º — R$ 67,54/filho |
| FGTS | Lei 8.036/90 — 8% sobre remuneração |
| DSR sobre variáveis | Lei 605/49 |
| Vale Transporte | Lei 7.418/85 — máx 6% salário base |
| Adicionais CCT | Norma mais favorável ao trabalhador |

### Sócios/Administradores (Pró-labore)
| Item | Regra |
|------|-------|
| INSS | 11% fixo (não progressivo) — Lei 8.212/91, Art. 21 |
| Teto INSS | R$ 8.475,55 → máx R$ 932,31 |
| INSS Patronal | 20% pago pela empresa — Art. 22 |
| RAT | 1%–3% conforme CNAE × FAP |
| IRRF | Mesma tabela CLT + redução Lei 15.270/2025 |
| Valor mínimo | 1 salário mínimo (R$ 1.621,00) |
| Não incide | FGTS, 13º, férias, DSR, HE, aviso prévio |

### Estagiários (Lei 11.788/2008)
| Item | Regra |
|------|-------|
| INSS | Não incide |
| IRRF | Isento — Lei 7.713/88, Art. 6º, V, "c" |
| FGTS | Não incide (sem vínculo empregatício) |
| Jornada máxima | 6h/dia e 30h/semana — Art. 10 |
| Recesso | 30 dias/ano proporcional — Art. 13 |
| Duração máxima | 2 anos na mesma empresa — Art. 11 |
| Seguro de vida | Obrigatório — Art. 9º, IV |

---

## 🚀 Token Optimization — Como Funciona

O sistema adapta o nível de detalhe do output ao tamanho da folha, maximizando economia sem jamais comprimir erros críticos.

```
┌────────────────────────────────────────────────────────┐
│              DECISÃO AUTOMÁTICA DE OUTPUT              │
├─────────────────┬──────────────────────────────────────┤
│ Pró-labore      │ Sempre compacto (4–6 rubricas)        │
│ ≤ 20 rubricas   │ Template completo (análise detalhada) │
│ 21–40 rubricas  │ Compacto (críticos + tabela resumo)   │
│ > 40 rubricas   │ Ultra-compacto (lotes + drill-down)   │
└─────────────────┴──────────────────────────────────────┘

Economia estimada com CCT de 100 páginas + 5 folhas:
  SEM otimização → estoura contexto na folha 3
  COM otimização → processa as 5 folhas com ~92k tokens/folha economizados
```

**Regras inegociáveis** — nunca comprimidos independente do tamanho:
- Erros críticos (desconto a maior, salário < mínimo/piso)
- Divergências > R$ 10,00
- Ausência de redução IRRF (Lei 15.270/2025)
- INSS calculado com regime errado
- Rubricas CLT em pró-labore

---

## 📊 Few-Shot Examples — Casos Cobertos

| # | Tipo | Cenário | Erro detectado |
|---|------|---------|---------------|
| 1 | CLT | INSS com alíquota fixa | Desconto R$ 48,48 a maior |
| 1b | Pró-labore | INSS 11% correto | Confirma regime adequado |
| 2 | CLT | IRRF sem redução Lei 15.270 | Desconto R$ 95,00 indevido |
| 2b | Pró-labore | IRRF sem redução | Desconto R$ 85,00 indevido |
| 3 | CLT | IRRF faixa intermediária | Divergência R$ 4,35 (arredondamento) |
| 4 | CLT | Salário Família desatualizado | Diferença R$ 11,00/filho |
| 5 | CLT | Salário abaixo piso CCT | Passivo de R$ 129,00/mês |
| 6 | Pró-labore | FGTS e 13º indevidos | Rubricas CLT em pró-labore |
| 7 | Pró-labore | Valor abaixo do mínimo | Risco previdenciário e fiscal |

---

## 🔧 Como Usar

### Pré-requisitos
- Acesso à API da Anthropic (Claude Sonnet 4.6 ou superior recomendado)
- Para folhas CLT: CCT do sindicato da categoria

### Setup Básico

```python
import anthropic

client = anthropic.Anthropic()

with open("system_prompt.txt", "r") as f:
    system_prompt = f.read()

# Fluxo recomendado para múltiplas folhas:
# 1. Primeira mensagem: envie a CCT em PDF
# 2. Segunda em diante: envie cada holerite

response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=4096,
    system=system_prompt,
    messages=[
        {
            "role": "user",
            "content": "Analise este holerite: [conteúdo do holerite]"
        }
    ]
)

print(response.content[0].text)
```

### Comandos do Usuário

```
# Durante análise de CCT
"extraia apenas o essencial"   → Resumo compacto (~3k tokens)
"CCT completa necessária"      → Mantém tudo

# Durante análise de folha
"resumo"                       → Apenas críticos + impacto total
"completo"                     → Template detalhado
"detalhe rubrica 401"          → Expande cálculo específico
"relatório final"              → Versão formatada completa
```

---

## 📁 Estrutura do Repositório

```
auditor-folha-pagamento/
├── README.md
├── system_prompt.txt          ← Prompt completo pronto para uso
├── examples/
│   ├── clt_simples.md         ← Exemplo folha ≤20 rubricas
│   ├── clt_grande.md          ← Exemplo folha >40 rubricas
│   ├── prolabore.md           ← Exemplo pró-labore
│   └── estagiario.md          ← Exemplo bolsa-auxílio
└── docs/
    ├── tabelas_2026.md        ← Tabelas INSS/IRRF 2026 isoladas
    └── changelog.md           ← Histórico de versões por lei
```

---

## 📅 Manutenção

Este prompt precisa ser atualizado anualmente (janeiro) com:
- Novo salário mínimo (Decreto de reajuste)
- Novas faixas INSS (Portaria MPS/MF)
- Novas faixas IRRF (se houver alteração)
- Novo valor Salário Família
- Novos tetos previdenciários

**Próxima revisão prevista:** Janeiro/2027

---

## 👤 Autor

**Carlos Torres** — AI Solutions Architect  
[LinkedIn](https://linkedin.com/in/carlostorressjr) · [GitHub](https://github.com/CTorressjr)

> Desenvolvido a partir de casos reais de auditoria de folha em escritório de contabilidade com 300+ empresas clientes.

---

## ⚠️ Aviso Legal

Este sistema é uma ferramenta de auxílio à auditoria. Os resultados devem ser validados por profissional habilitado. O autor não se responsabiliza por decisões tomadas exclusivamente com base no output do sistema sem revisão humana. A legislação trabalhista e previdenciária brasileira é complexa e sujeita a interpretações jurídicas que excedem o escopo deste prompt.
