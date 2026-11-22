# AcadFlow — Módulo de Acompanhamento Acadêmico (PPGEC/UPE)

Sistema web de gestão de processos e indicadores acadêmicos do Programa de Pós-Graduação em Engenharia da Computação (PPGEC) da Universidade de Pernambuco (UPE), em produção em `acadflow.poli.br`.

Este repositório é um fork da entrega da **Equipe E4** (Engenharia de Software – 2026.2), responsável pela concepção, desenvolvimento, testes e implantação em produção (v1.4.1) do **Módulo de Acompanhamento Acadêmico** (`acompanhamento/`).

---

## 🎯 Problema Resolvido e Valor Entregue

Antes do módulo, o controle de prazos regimentais (projeto de dissertação, qualificação e defesa) e o levantamento de indicadores de desempenho de turmas e docentes eram feitos manualmente fora do sistema.

O módulo integra-se ao AcadFlow para centralizar:
- **Monitoramento de Prazos:** Cálculo dinâmico de regularidade considerando prorrogações, trancamentos, conceitos e calendário acadêmico.
- **Alertas Automatizados:** Rotina assíncrona diária (às 7h) para notificar secretaria e coordenação sobre discentes irregulares.
- **Painéis Gerenciais e BI:** Indicadores de sucesso por turma, tramitação de processos, distribuição docente e desempenho por orientador.
- **Exportação de Dados:** Geração de planilhas Excel filtradas com auditoria de acesso.

---

## 👨‍💻 Minhas Contribuições no Projeto

Atuei na Equipe E4 como **Especialista BI & Desenvolvedor Backend**, contribuindo na modelagem dos indicadores gerenciais, regras de negócio em Python/Django e testes automatizados:

- **Serviço de Previsão de Conclusão por Orientador (`acompanhamento/previsao_conclusao.py`):**
  - Implementação da lógica de cálculo de previsão de defesa para discentes ativos por orientador e nível (Mestrado/Doutorado), integrando prorrogações, trancamentos e conversão dinâmica de datas em semestres letivos via `MarcoCalendario`.
  - Tratamento de casos de borda (prazos já vencidos e discentes sem data de ingresso) e criação da suíte de testes unitários isolados (`test_previsao_conclusao.py`).
- **Consistência Matemática em Indicadores (`acompanhamento/servicos.py`):**
  - Refinamento do cálculo de percentuais no **Painel de Sucesso por Turma** (`calcular_painel_sucesso_turma`), implementando redistribuição determinística de resíduos de arredondamento para garantir fechamento exato em `100,0%` nas categorias principais e consistência na subdivisão entre ativos regulares e irregulares.
  - Cobertura de testes automatizados para cenários de dízimas periódicas e empates (`test_painel_sucesso_turma.py`).
- **Indicadores Acadêmicos e Documentação Técnica:**
  - Definição de métricas gerenciais junto à coordenação/secretaria e elaboração da documentação técnica, regras de negócio e passagem de conhecimento do módulo (`docs/`).
- **Fluxo Colaborativo & Qualidade:**
  - Desenvolvimento orientado a Pull Requests com commits convencionais, revisão de código entre pares e validação via suíte de testes Django (`270+` testes automatizados).

---

## 🛠️ Stack Tecnológica

- **Backend:** Python 3.11 / 3.12, Django 5.2
- **Banco de Dados:** PostgreSQL 16 (produção/CI) e SQLite (desenvolvimento local)
- **Processamento Assíncrono:** Celery 5.4, Redis 6.1, Kombu 5.6
- **Relatórios e Armazenamento:** OpenPyXL, WhiteNoise, Django Storages (AWS S3)
- **Infraestrutura e CI/CD:** Docker, Docker Compose, Gunicorn, GitHub Actions (CI e Ensaio de Produção)

---

## 🏗️ Arquitetura do Módulo

O módulo foi construído como um aplicativo Django desacoplado (`acompanhamento/`), controlado por *feature flags* (`ACOMPANHAMENTO_ATIVO` e `ACOMPANHAMENTO_AVISO_ATIVO`) para ativação segura em produção sem impacto nas rotas preexistentes:

```text
acompanhamento/
├── models.py               # Calendário de marcos, avisos e logs de auditoria
├── situacao.py             # Motor centralizado de cálculo de prazos e regularidade
├── previsao_conclusao.py   # Cálculo de previsão de conclusão por orientador
├── servicos.py             # Agregações e indicadores dos painéis gerenciais
├── lista_alunos.py         # Preparação de listagens e cartões de alerta
├── planilha_alunos.py      # Exportação de relatórios em Excel (.xlsx)
├── avisos.py & tasks.py    # Processamento e agendamento Celery de e-mails diários
├── permissoes.py           # Controle de acesso baseado nos papéis do PPGEC
└── tests/                  # Suíte completa de testes unitários e de integração
```

---

## 🚀 Como Executar Localmente

### 1. Clonar e configurar o ambiente virtual
```bash
git clone https://github.com/AntoniooAlbuquerque/acadflow-ppgec.git
cd acadflow-ppgec
python -m venv .venv
source .venv/bin/activate  # No Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

### 2. Configurar variáveis de ambiente
```bash
cp .env.example .env
```
Para desenvolvimento rápido com SQLite local, garanta que no `.env` conste:
```env
DEBUG=True
USE_POSTGRES=False
ACOMPANHAMENTO_ATIVO=True
```

### 3. Aplicar migrations, gerar dados fictícios e iniciar
```bash
python manage.py migrate
python manage.py gerar_dados_ficticios --semente 42
python manage.py runserver
```
Acesse `http://localhost:8000/acompanhamento/`.

### 4. Executar testes automatizados
```bash
python manage.py test acompanhamento
```

---

## 📚 Documentação Completa

Toda a documentação de engenharia produzida pela equipe está disponível no diretório `docs/`:
- [Documento de Negócio](docs/documento-de-negocio.md) — Regras acadêmicas, requisitos e indicadores.
- [Documento Técnico](docs/entrega-modulo-acompanhamento.md) — Arquitetura, infraestrutura, CI/CD e deploy.
- [Passagem de Conhecimento](docs/passagem-de-conhecimento.md) — Decisões arquiteturais e guia de continuidade.
- [Manual de Uso da Secretaria](docs/manual-de-uso-secretaria.md) — Guia operacional para usuários finais.
- [Guia do Ambiente Local](docs/guia-do-ambiente-local.md) — Setup detalhado com PostgreSQL, Redis e Docker.
