# Despensa Viva

[![Status](https://img.shields.io/badge/status-[em_desenvolvimento]-yellow)]()
[![Versão](https://img.shields.io/badge/versão-[0.0.0]-blue)]()
[![Licença](https://img.shields.io/badge/licença-[acadêmica]-lightgrey)]()

**Instituição:** CEUB  
**Curso:** Análise e Desenvolvimento de Sistemas  
**Disciplina:** Desenvolvimento Web  
**Turma / Semestre:** Turma A / 2026.2  
**Professor(a):** Felippe Pires Ferreira  
**Status do projeto:** Fase 1 (documentação e arquitetura)

---

## Sumário

- [1. Descrição do projeto](#1-descrição-do-projeto)
- [2. Funcionalidades](#2-funcionalidades)
- [3. Demonstração](#3-demonstração)
- [4. Tecnologias utilizadas](#4-tecnologias-utilizadas)
- [5. Arquitetura](#5-arquitetura)
- [6. Organização dos diretórios](#6-organização-dos-diretórios)
- [7. Participantes](#7-participantes)
- [8. Como executar](#8-como-executar)
- [9. Configuração](#9-configuração)
- [10. Testes](#10-testes)
- [11. Uso de inteligência artificial](#11-uso-de-inteligência-artificial)
- [12. Contribuição e fluxo de trabalho](#12-contribuição-e-fluxo-de-trabalho)
- [13. Histórico de versões](#13-histórico-de-versões)
- [14. Limitações e próximos passos](#14-limitações-e-próximos-passos)
- [15. Licença, referências e contato](#15-licença-referências-e-contato)

---

## 1. Descrição do projeto

Famílias e pessoas que moram sozinhas costumam controlar os alimentos de forma informal, sem registro do que existe, onde está guardado ou quando vence. O resultado são compras repetidas, produtos esquecidos no fundo do armário e desperdício por vencimento, com impacto no orçamento doméstico e no meio ambiente.

A Despensa Viva é uma aplicação web que centraliza o estoque doméstico. O morador cadastra locais de armazenamento (geladeira, armário etc.), produtos e itens com quantidade e validade, registra consumo e descarte, e acompanha alertas de itens vencidos ou a vencer. Um relatório consolida os indicadores da despensa, com exportação em CSV e versão para impressão.

Para tornar o cadastro rápido, o sistema consome a API pública Open Food Facts: ao informar o código de barras, nome, marca, Nutri-Score e dados nutricionais são preenchidos automaticamente. Uma API REST própria permite que terceiros consultem os dados do estoque.

### Objetivos

*Liste os objetivos gerais e específicos do projeto.*

- **Objetivo geral:** desenvolver uma aplicação web que reduza o desperdício de alimentos domésticos por meio do controle de estoque e de validades.
- **Objetivos específicos:**
  - permitir cadastro e autenticação de usuários, com isolamento dos dados de cada um;
  - cadastrar, consultar, alterar e excluir locais, produtos e itens de estoque;
  - preencher dados de produtos a partir do código de barras (API externa);
  - alertar sobre itens vencidos e a vencer;
  - gerar relatório consolidado, exportável e imprimível;
  - expor uma API REST documentada;
  - publicar a aplicação com HTTPS e submetê-la a análises SAST e DAST.

### Público-alvo

- Moradores de residências (famílias, estudantes, repúblicas)
- Pessoas que desejam organizar compras e reduzir desperdício

---

## 2. Funcionalidades

*Liste as funções implementadas (ou previstas) no sistema. Marque o status de cada uma.*

| Funcionalidade | Descrição | Status |
| --- | --- | --- |
| Autenticação (RF01) | Cadastro, login e logout de usuários | Planejada |
| Categorias (RF03) | Manutenção de categorias pelo administrador | Planejada |
| Produtos (RF04) | CRUD do catálogo de produtos | Planejada |
| Consulta por código de barras (RF05) | Preenchimento automático via Open Food Facts | Planejada |
| Itens de estoque (RF06) | CRUD de itens com quantidade, validade e preço | Planejada |
| Consumo e descarte (RF07) | Baixa de estoque com histórico de movimentações | Planejada |
| Busca e filtros (RF08) | Busca por nome, marca, código, categoria, local e validade | Planejada |
| Painel de alertas (RF09) | Itens vencidos e a vencer em 7 dias | Planejada |
| Relatório (RF10) | Indicadores consolidados, exportação CSV e impressão | Planejada |
| API REST (RF11) | API v1 com token, filtros, paginação e OpenAPI | Planejada |

### Requisitos não funcionais

*Informe restrições de qualidade, quando existirem.*

- **Desempenho:** páginas comuns em até 2 s com até 5.000 itens por usuário; timeout de 5 s na API externa.
- **Segurança:** senhas com hash do Django; HTTPS e DEBUG=False em produção; segredos em variáveis de ambiente; limite de requisições na API; análises SAST e DAST.
- **Usabilidade:** interface responsiva para desktop e celular, com identidade visual própria.
- **Disponibilidade:** aplicação publicada em URL pública durante o período de avaliação.

---

## 3. Demonstraçãom (Em desenvolvimento...)

*Inclua capturas de tela, GIF ou link para vídeo. Coloque as imagens em `images/`.*

![Tela principal](images/[screenshot-principal].png)

| Tela | Descrição |
| --- | --- |
| [Login] | [Acesso ao sistema com e-mail e senha] |
| [Painel] | [Visão geral das reservas do dia] |

**Vídeo / protótipo:** [URL do YouTube, Loom ou Figma]

---

## 4. Tecnologias utilizadas

Tecnologias previstas para a implementação (Fase 2):

| Camada | Tecnologia | Versão |
| --- | --- | --- |
| Linguagem | Python | 3.12 |
| Frontend | Django Templates, HTML, CSS, Bootstrap | 5.x |
| Backend | 	Django, Django REST Framework, drf-spectacular | 5.x |
| Banco de dados | 	PostgreSQL (produção) e SQLite (desenvolvimento) | 16 / 3 |
| Testes | 	Django TestCase / pytest | [] |
| Infraestrutura | [] | — |
| Outras ferramentas | Git, GitHub, requests | — |

---

## 5. Arquitetura

A solução é um monolito Django modular. Páginas HTML renderizadas no servidor e uma API REST JSON compartilham os mesmos models e regras de negócio. Um módulo de integração isola a comunicação com a Open Food Facts, e os produtos consultados ficam salvos no catálogo local (cache). A escolha por monolito atende ao tamanho da equipe, ao prazo e à facilidade de implantação.

```text
[Morador] → [Templates + Bootstrap] ┐
                                    ├→ [Apps Django: accounts | inventory | reports | api] → [PostgreSQL]
[Terceiro] → [API REST /api/v1/]  ──┘                         │
                                                              └→ [integrations] → [Open Food Facts]
```

**Decisões relevantes:**

- Monolito modular, pela simplicidade operacional e pelo reuso de regras entre web e API.
- Persistência relacional (PostgreSQL), porque os dados têm relacionamentos bem definidos (usuário, local, produto, item, movimentação).
- Product funciona como cache da API externa, reduzindo chamadas e mantendo o sistema operando se a API cair.
- Isolamento por usuário: toda consulta filtra os registros do dono autenticado.

### Endpoints principais (quando houver API)

| Método | Rota | Descrição |
| --- | --- | --- |
| `POST` | `/api/[recurso]` | [Ex.: criar um registro] |
| `GET` | `/api/[recurso]` | [Ex.: listar registros] |
| `GET` | `/api/[recurso]/{id}` | [Ex.: obter um registro] |
| `PUT` | `/api/[recurso]/{id}` | [Ex.: atualizar um registro] |
| `DELETE` | `/api/[recurso]/{id}` | [Ex.: remover um registro] |

Documentação completa da API: [link para Swagger, Postman ou `docs/api.md`]

---

## 6. Organização dos diretórios

*Mantenha a árvore alinhada à estrutura real do repositório. Ajuste pastas conforme o tipo de projeto.*

```text
.
├── README.md                          # Documentação principal do projeto
├── .env.example                       # Modelo de variáveis de ambiente (Fase 2)
├── .gitignore
├── images/                            # Figuras da documentação geral (ex.: política de IA)
├── docs/
│   ├── rastreabilidade.md             # Matriz requisitos × casos de uso × modelo × API
│   ├── visao/                         # Documento de Visão
│   ├── casos-de-uso/                  # Diagrama UML (fonte, SVG, PNG) e especificações
│   ├── arquitetura/                   # Diagrama de componentes e texto de arquitetura
│   ├── banco-de-dados/                # Modelo ER (fontes, SVG, PNG) e dicionário de dados
│   ├── api/                           # Contrato da API própria e plano de integração externa
│   ├── prototipos/                    # Identidade visual, logotipo e wireframes
│   ├── planejamento/                  # Backlog, responsáveis, marcos e riscos
│   ├── diagramas/                     # Fontes editáveis (.puml, .mmd) e exportações
│   └── seguranca/                     # Evidências SAST/DAST (Fase 2)
├── src/                               # (Fase 2) Projeto Django
├── tests/                             # (Fase 2) Testes automatizados
└── scripts/                           # (Fase 2) Scripts auxiliares
```

| Diretório / arquivo | Função |
| --- | --- |
| `README.md` | Apresentação do projeto, objetivos, tecnologias e instruções de uso |
| `docs/visao/` | Contexto, objetivos, escopo, restrições, riscos e critérios de sucesso |
| `docs/casos-de-uso/` | Diagrama UML e especificação textual dos casos de uso |
| `docs/arquitetura/` | Componentes, camadas, fluxo de dados e justificativas |
| `docs/banco-de-dados/` | Diagrama ER e dicionário de dados |
| `docs/api/` | Contrato da API REST e plano de integração com a Open Food Facts |
| `docs/prototipos/` | Nome, paleta, tipografia, logotipo e protótipos |
| `docs/planejamento/` | Backlog, marcos e riscos até a Fase 2 |
| `docs/diagramas/` | Arquivos-fonte editáveis e exportações dos diagramas |
| `docs/seguranca/` | Relatórios e evidências de SAST/DAST |

---

## 7. Participantes

| Nome | Matrícula | Função no projeto |
| --- | --- | --- |
| José Neto | 22502693 | Back-end e API, integração externa, infraestrutura e segurança |
| Matheus Covre | [000000] | Front-end e templates, relatórios, testes e identidade visual |

**Professor(a) responsável:** Felippe Pires Ferreira
---

## 8. Como executar (Em desenvolvimento...)

*Preencha com os comandos reais do projeto para que outra pessoa consiga reproduzir o ambiente.*

### Pré-requisitos (Em desenvolvimento...)

- [Ex.: Git]
- [Ex.: Python 3.12+]
- [Ex.: Node.js 20+]
- [Ex.: Docker]

### Instalação e execução (Em desenvolvimento...)

```bash
# 1. Clonar o repositório
git clone [URL_DO_REPOSITORIO]
cd [NOME_DA_PASTA]

# 2. Instalar dependências
[comando de instalação]

# 3. Configurar variáveis de ambiente
cp .env.example .env
# edite o arquivo .env com as credenciais locais

# 4. Executar a aplicação
[comando de execução]
```

**Acesso local:** [Ex.: http://localhost:3000]

### Implantação (quando houver)

- **Ambiente:** [Ex.: Render, Railway, Vercel, servidor da instituição]
- **URL de produção:** [https://...]
- **Observações:** [Ex.: é necessário configurar as variáveis de ambiente no painel do provedor]

---

## 9. Configuração (Em desenvolvimento...)

*Liste as variáveis de ambiente usadas pelo sistema. Nunca publique senhas, tokens ou chaves neste arquivo.*

| Variável | Obrigatória | Descrição | Exemplo |
| --- | --- | --- | --- |
| `PORT` | Sim | Porta da aplicação | `3000` |
| `DATABASE_URL` | Sim | Conexão com o banco | `postgresql://user:senha@localhost:5432/app` |
| `SECRET_KEY` | Sim | Chave de sessão / JWT | `[gerar localmente]` |

Credenciais reais devem ficar apenas no arquivo `.env` (não versionado).

---

## 10. Testes (Em desenvolvimento...)

*Descreva como executar os testes e o que eles cobrem.*

```bash
[comando para executar os testes]
```

| Tipo | Ferramenta | O que verifica |
| --- | --- | --- |
| Unitários | [Ex.: pytest / JUnit / Jest] | [Ex.: regras de negócio isoladas] |
| Integração | [Ex.: ...] | [Ex.: API e banco de dados] |
| Manuais | [Ex.: checklist em `docs/`] | [Ex.: fluxos principais da interface] |

**Cobertura atual:** [Ex.: 70% / não medida]

---

## 11. Uso de inteligência artificial

Este repositório segue a política de uso de IA da disciplina (semáforo pedagógico):

![Política de uso de IA — semáforo](images/semaforo.png)

| Situação | Significado |
| --- | --- |
| **Vermelho — uso proibido** | Atividades de autonomia intelectual (ex.: provas presenciais sem consulta). |
| **Amarelo — uso limitado** | IA pode ser ferramenta auxiliar, desde que haja declaração de uso. |
| **Verde — uso permitido** | Uso livre ao longo da atividade acadêmica. |

### Declaração de uso

*Preencha de forma honesta. Se não houve uso de IA, declare explicitamente.*

- **Houve uso de IA neste projeto?** Sim
- **Ferramentas utilizadas:** 
- **Finalidade:** elaboração do rascunho da documentação da Fase 1, revisão gramátical, formatação de texto.
- **O que NÃO foi delegado à IA:** definição do problema, modelagem, implementação das regras de negócio

---

## 12. Contribuição e fluxo de trabalho

*Padronize o trabalho em equipe. Ajuste as regras ao combinado da disciplina.*

### Branches

- `main` — versão estável para avaliação
- `develop` — integração do grupo *(opcional)*
- `feat/[nome]` — nova funcionalidade
- `fix/[nome]` — correção de defeito
- `docs/[nome]` — alterações só de documentação

### Commits

Use mensagens curtas e no imperativo, por exemplo:

- `feat: adiciona cadastro de reservas`
- `fix: corrige validação de data`
- `docs: atualiza instruções de execução`

### Passos sugeridos

1. Criar uma branch a partir de `main`.
2. Implementar e testar localmente.
3. Abrir um *pull request* / *merge request* para revisão do grupo.
4. Só então integrar à branch principal.

**Issues e quadro de tarefas:** [link do GitHub Projects, Trello ou similar]

---

## 13. Histórico de versões

*Registre entregas relevantes (sprints, checkpoints ou versões avaliadas).*

| Versão | Data | Descrição |
| --- | --- | --- |
| `0.0.1` | 2026-10-03 | Estrutura inicial do repositório e documentação da Fase 1 |


---

## 14. Limitações e próximos passos

### Problemas conhecidos

- A aplicação ainda não foi implementada; este repositório contém apenas a documentação e a arquitetura (Fase 1).
- A leitura de código de barras pela câmera, notificações por e-mail/push e o compartilhamento de despensa entre usuários estão fora do escopo.
- Nem todo produto brasileiro existe na base da Open Food Facts; nesses casos o cadastro é manual.

### Roadmap

- [x] Documentação e arquitetura (Fase 1)
- [ ] Projeto Django, models, migrations e autenticação
- [ ] CRUD, busca, painel de alertas e identidade visual
- [ ] Integração com a Open Food Facts
- [ ] API REST e documentação OpenAPI
- [ ] Relatório com exportação CSV e impressão
- [ ] Testes automatizados
- [ ] Publicação com HTTPS
- [ ] SAST e DAST, correções e relatório de segurança

---

## 15. Licença, referências e contato

**Licença:** uso exclusivamente acadêmico.

Este material destina-se a fins educacionais. Verifique com a disciplina se o código pode ser reutilizado fora do curso.

### Documentação complementar

- Índice da pasta `docs/`: [`docs/README.pdf`](docs/README.pdf)
- Casos de uso (diagrama + especificações): [`docs/modelagem/casos-de-uso/especificacoes-casos-de-uso.pdf`](docs/modelagem/casos-de-uso/especificacoes-casos-de-uso.pdf)
- Diagrama de classes: [`docs/modelagem/classes/diagrama-de-classes.pdf`](docs/modelagem/classes/diagrama-de-classes.pdf)
- Modelo conceitual (ER): [`docs/modelagem/banco-de-dados/diagrama-er.pdf`](docs/modelagem/banco-de-dados/diagrama-er.pdf)
- Modelo lógico: [`docs/modelagem/banco-de-dados/modelo-logico.pdf`](docs/modelagem/banco-de-dados/modelo-logico.pdf)
- Apresentação: [`docs/apresentacao.pdf`](docs/)

### Referências

- Open Food Facts. API Documentation. https://openfoodfacts.github.io/openfoodfacts-server/api/
- Django Software Foundation. Django Documentation. https://docs.djangoproject.com/
- Django REST Framework. https://www.django-rest-framework.org/
- drf-spectacular. https://drf-spectacular.readthedocs.io/
- OWASP. OWASP ZAP. https://www.zaproxy.org/

### Contato

Dúvidas sobre o projeto: e-mail institucional (José Neto): netonovais@sempreceub.com

**Agradecimentos:** Professor Felippe Pires Ferreira
