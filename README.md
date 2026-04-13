# FIAP Connect — Componente de Inteligência Artificial

## Sprint 3 — Planejamento e Validação do Modelo de IA

### Integrantes

| Nome | RM | Turma |
|------|----|-------|
| Alexis Rondo | 560384 | 2TDSPS |
| Vinicius Rodrigues de Oliveira | 559611 | 2TDSPS |

---

## Problema

O **FIAP Connect** é uma plataforma para formação inteligente de grupos para o Challenge Sprint da FIAP. Cada grupo precisa cobrir 7 disciplinas (MOBILE, JAVA, DEVOPS, BD, DOTNET, IOT, SQA) com no máximo 3 integrantes.

Atualmente, a busca de grupos usa regras fixas no banco Oracle que calculam quantas disciplinas um aluno cobre em relação ao que o grupo precisa. Essa lógica é determinística e não aprende com o tempo.

## Solução de IA proposta

Treinar um **modelo de classificação supervisionada** (Machine Learning) que analise múltiplos atributos de um aluno e de um grupo para prever o nível de compatibilidade: **ALTA**, **MEDIA** ou **BAIXA**.

## Modelo escolhido

- **Random Forest Classifier** (sklearn)
- Comparado com KNN para validação

### Justificativa

- Algoritmo de classificação supervisionada adequado para dados tabulares
- Lida bem com atributos numéricos e categóricos
- Resultados interpretáveis
- Estudado em aula na disciplina

## Dados utilizados

Os dados são derivados das tabelas do Oracle APEX (banco do FIAP Connect):

| Tabela | Dados extraídos |
|--------|----------------|
| USUARIO | período, unidade, habilidades do aluno |
| GRUPO | vagas disponíveis, disciplinas faltantes |
| HABILIDADE | as 7 disciplinas do Challenge |
| USUARIO_HABILIDADE | portfólio do aluno |
| GRUPO_USUARIO_HABILIDADE | disciplinas assumidas no grupo |

### Features (atributos de entrada)

| Feature | Descrição |
|---------|-----------|
| num_habilidades_aluno | Quantas disciplinas o aluno domina (1-7) |
| num_habilidades_faltantes_grupo | Disciplinas que faltam no grupo (0-7) |
| num_cobertas | Disciplinas do aluno que coincidem com as faltantes |
| percentual_cobertura | num_cobertas / faltantes * 100 |
| mesmo_periodo | 1 se mesmo horário (MANHA/NOITE) |
| mesma_unidade | 1 se mesma unidade (PAULISTA/ACLIMACAO) |
| vagas_disponiveis | Vagas restantes no grupo (1 ou 2) |

### Variável alvo

- `compatibilidade`: ALTA, MEDIA ou BAIXA

## Resultados parciais (Sprint 3)

| Modelo | Acurácia |
|--------|----------|
| Random Forest | ~85% |
| KNN | ~75% |

O Random Forest obteve melhor desempenho e será o modelo utilizado na integração.

## Diagrama de Integração (Sprint 4)

```
┌─────────────────┐    ┌──────────────────┐    ┌────────────────────┐
│   App Mobile    │    │   Oracle APEX    │    │   Oracle Database  │
│  (React Native) │───>│   (REST API)     │───>│   (USUARIO, GRUPO, │
│                 │    │                  │    │    HABILIDADE...)   │
│  Tela de busca  │    │  GET /grupos-    │    │                    │
│  de grupos      │    │  compativeis/:rm │    │                    │
└─────────────────┘    └────────┬─────────┘    └────────────────────┘
                                │
                                │ POST /predict
                                v
                       ┌──────────────────┐
                       │   API Flask      │
                       │   (Docker)       │
                       │                  │
                       │  modelo.pkl      │
                       │  (Random Forest) │
                       │                  │
                       │  Retorna JSON:   │
                       │  {"predicao":    │
                       │   "ALTA"}        │
                       └──────────────────┘
```

## Fluxo de funcionamento

1. Aluno abre o app mobile e busca grupos compatíveis
2. App chama API REST do Oracle APEX
3. APEX consulta banco Oracle (dados do aluno e grupos disponíveis)
4. APEX monta os atributos e envia via POST para a API Flask
5. Flask carrega o modelo `.pkl` e executa a predição
6. Flask retorna a compatibilidade (ALTA/MEDIA/BAIXA) em JSON
7. APEX retorna grupos ordenados por compatibilidade
8. App mobile exibe os resultados

## Tecnologias

- Python 3.11
- Scikit-learn (Random Forest, KNN)
- Pandas / NumPy
- Google Colab (treinamento)
- Flask (API — Sprint 4)
- Docker (containerização — Sprint 4)
- Oracle APEX (integração — Sprint 4)

## Estrutura do repositório

```
fiap-connect-ia/
├── README.md
├── FIAP_Connect_IA_Compatibilidade_Sprint3.ipynb
├── modelo_compatibilidade.pkl    (gerado pelo notebook)
└── label_encoder.pkl             (gerado pelo notebook)
```

## Como executar

1. Abra o notebook no Google Colab
2. Execute todas as células em sequência
3. O modelo será treinado e testado automaticamente
4. Os arquivos `.pkl` serão gerados na última célula

## Vídeo de apresentação

[Link do YouTube — não listado]
