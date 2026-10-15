# Monitor de Vagas n8n

Sistema automatizado desenvolvido no n8n para monitoramento e classificacao de vagas de tecnologia da informacao em tempo real, com notificacoes via Telegram e organizacao por categorias profissionais.

## Arquitetura

```mermaid
flowchart TD
    Schedule["Schedule Trigger: 09:00 e 15:00"] --> FetchDev["Buscar Vagas Dev Remoto: JSearch"]
    Schedule --> FetchSup["Buscar Vagas Suporte Remoto: JSearch"]
    Schedule --> FetchTIFSA["Buscar Vagas TI FSA: JSearch"]
    Schedule --> FetchLI["Buscar Posts LinkedIn: Apify"]
    
    FetchDev --> MergeDS["Combinar Dev e Suporte: Merge"]
    FetchSup --> MergeDS
    MergeDS --> MergeJS["Combinar Todas JSearch: Merge"]
    FetchTIFSA --> MergeJS
    
    MergeJS --> SplitJS["Separar Vagas JSearch: Code"]
    FetchLI --> NormLI["Normalizar Posts LinkedIn: Code"]
    
    SplitJS --> MergeAll["Combinar Todas as Vagas: Merge"]
    NormLI --> MergeAll
    
    MergeAll --> Classifier["Classificador e Deduplicacao"]
    Classifier --> Switch{"Switch Classificacao"}
    
    Switch -->|TI Feira de Santana| TG1["Telegram: TI Feira de Santana"]
    Switch -->|Desenvolvedor Remoto| TG2["Telegram: Dev Remoto"]
    Switch -->|Outras Areas TI| TG3["Telegram: Outras Areas TI"]
```

## Categorias e Regras de Negocio

1. TI Feira de Santana: Vagas locais presenciais ou hibridas na area de tecnologia em Feira de Santana, Bahia.
2. Desenvolvedor Remoto: Vagas de estagio e posicoes junior em desenvolvimento de software em modalidade remota para todo o Brasil.
3. Outras Areas TI: Vagas remotas em suporte, dados, qualidade (QA) e infraestrutura.

## Recursos Tecnicos

- Multiplas fontes: integracao oficial de vagas de emprego (RapidAPI JSearch) combinada com raspagem de posts informais de recrutadores no feed do LinkedIn (Apify).
- Tag de identificacao de origem: distincao clara entre processos formais (`Tipo: VAGAS`) e publicacoes diretas no feed (`Tipo: POST`).
- Deduplicacao dupla e semantica: memoria persistente por ID unico (`seenJobIds`) e por impressao digital cruzada (`seenFingerprints`), eliminando republicacoes identicas entre diferentes portais (Gupy, Catho, InfoJobs, LinkedIn).
- Match Score de compatibilidade: classificacao automatica de aderencia (ALTA, MEDIA, GERAL) analisando a presenca da stack de interesse (Python, JavaScript, TypeScript, React, Node.js, SQL, Docker, Git).
- Filtro Anti-Vagas Fantasmas: descarte automatico e previo de bancos de talentos, cadastros de reserva, empresas confidenciais, posts isca de engajamento no LinkedIn ('comente eu quero') e descricoes ocas/vazias sem requisitos tecnicos.
- Filtro Anti-Falso Junior: deteccao automatica de requisitos abusivos de experiencia (3+ anos, nivel pleno/senior) em oportunidades de estagio ou junior, com emissao de alerta na notificacao.
- Botoes interativos no Telegram: botoes nativos (Inline Keyboard) para abertura direta da vaga ou do post no LinkedIn com um clique, mantendo a mensagem limpa e ergonomica no celular.
- Execucao automatica programada para 09:00 e 15:00 BRT.
- Filtro de idade de vagas: descarta automaticamente qualquer oportunidade publicada ha mais de 7 dias para evitar candidaturas em processos seletivos antigos.
- Exibicao de data formatada e indicador de recuo temporal nas mensagens do Telegram.
- Roteamento por no Switch para direcionar cada vaga para sua mensagem correspondente.
- Mensagens estruturadas no Telegram com bloco de codigo para copia direta de prompts voltados a IA.
- Auto-limpeza (auto-pruning) com limite de registros para manutencao sustentavel no plano gratuito Always Free da Oracle Cloud.
- Tolerancia a falhas configurada para execucao continua sem interrupcoes.

## Tecnologias

- n8n (hospedado na Oracle Cloud Always Free)
- JavaScript ES6
- JSearch API via RapidAPI
- Apify API (Scraper de posts do LinkedIn)
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
2. Escolha o arquivo workflows/monitor_de_vagas.json.
3. Configure as credenciais da RapidAPI, Apify e do Telegram.
4. Ative o fluxo pelo botao Publish.

Autor: Jonathas Girardi
Repositorio: https://github.com/Jntgirardi/monitor-de-vagas-n8n
