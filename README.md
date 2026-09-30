# Monitor de Vagas n8n

Sistema automatizado desenvolvido no n8n para monitoramento e classificacao de vagas de emprego em tempo real, com notificacoes via Telegram e organizacao por categorias profissionais.

## Arquitetura

```mermaid
flowchart TD
    Schedule["Schedule Trigger: 09:00 e 15:00"] --> Fetch["Buscar Vagas: API JSearch"]
    Fetch --> Split["Separar Vagas: Split Out"]
    Split --> Classifier["Classificador e Deduplicacao"]
    Classifier --> Switch{"Switch Classificacao"}
    
    Switch -->|TI Feira de Santana| TG1["Telegram: TI Feira de Santana"]
    Switch -->|Desenvolvedor Remoto| TG2["Telegram: Dev Remoto"]
    Switch -->|Outras Areas TI| TG3["Telegram: Outras Areas TI"]
    Switch -->|Auxiliar Admin CLT FSA| TG4["Telegram: Admin CLT Feira de Santana"]
```

## Categorias e Regras de Negocio

1. TI Feira de Santana: Vagas locais presenciais ou hibridas na area de tecnologia em Feira de Santana, Bahia.
2. Desenvolvedor Remoto: Vagas de estagio em desenvolvimento de software em modalidade remota para todo o Brasil.
3. Outras Areas TI: Vagas remotas em suporte, dados, qualidade e infraestrutura.
4. Auxiliar Administrativo CLT: Vagas exclusivamente presenciais em Feira de Santana com contratacao formal CLT. Vagas remotas, MEI ou PJ sao descartadas automaticamente.

## Recursos Tecnicos

- Execucao automatica programada para 09:00 e 15:00.
- Deduplicacao em memoria para impedir o reenvio de vagas ja notificadas.
- Mensagens estruturadas no Telegram com bloco de codigo para copia direta de prompts.
- Tolerancia a falhas configurada para execucao continua sem interrupcoes.

## Tecnologias

- n8n
- JavaScript ES6
- JSearch API via RapidAPI
- Telegram Bot API
- Docker e Docker Compose

## Execucao Local

### 1. Clonar o repositorio
```bash
git clone https://github.com/Jntgirardi/monitor-de-vagas-n8n.git
cd monitor-de-vagas-n8n
```

### 2. Iniciar o n8n
Com Docker Compose:
```bash
docker compose up -d
```

Ou com Node.js:
```bash
npx n8n
```

Acesso local disponivel em: http://localhost:5678

### 3. Importar o Fluxo
1. No menu do n8n, selecione Add Workflow e clique em Import from File.
2. Escolha o arquivo workflows/linkedin_job_monitor.json.
3. Configure as credenciais da RapidAPI e do Telegram.
4. Ative o fluxo pelo botao Publish.

Autor: Jonathas Girardi
Repositorio: https://github.com/Jntgirardi/monitor-de-vagas-n8n
