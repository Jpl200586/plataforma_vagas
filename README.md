# Plataforma de Vagas e Candidaturas — Frontend

Este diretório contém a implementação do **frontend** do Projeto Integrador
(Plataforma de Vagas e Candidaturas), desenvolvida de forma independente do
backend (que será composto por microserviços REST: `empresas`, `vagas`,
`candidatos` e `candidaturas`).

> O frontend foi construído para ser integrado posteriormente ao
> repositório oficial da equipe. Toda a comunicação com a API passa por um
> único arquivo (`frontend/js/api.js`), então apontar para o backend real
> exigirá, no máximo, ajustar as URLs ali ou via `localStorage` (ver abaixo).

## Estrutura

```
plataforma-vagas/
├── frontend/
│   ├── index.html                Home (dashboard simples)
│   ├── vagas.html                Lista de vagas (com filtros)
│   ├── vaga-detalhes.html        Detalhes da vaga + dados da empresa
│   ├── empresa-cadastro.html     Cadastro de empresa
│   ├── vaga-cadastro.html        Cadastro de vaga
│   ├── candidato-cadastro.html   Cadastro de candidato (+ URL de currículo)
│   ├── candidatos.html           Lista de candidatos
│   ├── candidatura.html          Formulário de candidatura
│   ├── css/style.css             Estilos complementares ao Skeleton CSS
│   └── js/
│       ├── api.js                Camada central de acesso à API
│       ├── ui.js                 Cabeçalho/rodapé, alertas, formatação
│       └── *.js                  Lógica específica de cada página
├── cypress/e2e/                  Testes end-to-end (Cypress)
├── cypress.config.js
└── package.json                  Dependências de teste (Cypress, http-server)
```

## Como rodar localmente

O frontend é 100% estático (HTML/CSS/JS puro), não requer build. Para
visualizar:

1. Abra `frontend/index.html` diretamente no navegador, **ou**
2. Sirva a pasta com um servidor estático (recomendado, evita problemas de
   CORS ao consumir a API):

   ```bash
   npm install
   npm run serve:frontend
   # abre em http://localhost:5500
   ```

## Integração com o backend

As URLs base de cada microserviço ficam centralizadas em
`frontend/js/api.js`:

```js
const API_BASE = {
  empresas: "http://localhost:3001",
  vagas: "http://localhost:3002",
  candidatos: "http://localhost:3003",
  candidaturas: "http://localhost:3004",
};
```

Quando o backend da equipe estiver disponível, basta:

- editar essas constantes para os endereços reais, **ou**
- sobrescrevê-las sem editar código, via console do navegador:

  ```js
  localStorage.setItem("API_BASE_EMPRESAS", "http://localhost:3001");
  localStorage.setItem("API_BASE_VAGAS", "http://localhost:3002");
  localStorage.setItem("API_BASE_CANDIDATOS", "http://localhost:3003");
  localStorage.setItem("API_BASE_CANDIDATURAS", "http://localhost:3004");
  ```

Todas as páginas consomem a API exclusivamente através de `window.Api`
(`Api.empresas.listar()`, `Api.vagas.criar(dados)`, etc.), então não há
necessidade de alterar os arquivos de cada página.

## Testes automatizados (Cypress)

Os testes e2e simulam o fluxo real do usuário (Home → lista de vagas →
detalhes → candidatura) e cenários de erro (formulário inválido, API
retornando HTTP 400). As respostas da API são interceptadas com
`cy.intercept`, então os testes **não dependem do backend estar no ar**.

```bash
npm install
npm run test:e2e        # sobe o servidor estático e roda os testes em modo headless
# ou, para desenvolvimento interativo:
npm run serve:frontend  # em um terminal
npm run cypress:open    # em outro terminal
```

## Funcionalidades implementadas

- Home com totais (vagas abertas, empresas, candidatos);
- Lista de vagas com busca por título, cidade, modalidade e empresa;
- Detalhes da vaga com dados da empresa responsável e ação "Candidatar-se";
- Cadastro de empresa, vaga e candidato, com validação client-side;
- Lista de candidatos, com exclusão e link para currículo (desafio extra);
- Formulário de candidatura, com vaga pré-selecionada via querystring;
- Tratamento de erros de rede e de API (400/404/etc.) com mensagens amigáveis;
- Interface responsiva baseada em Skeleton CSS.

## Pendências / próximos passos (ao integrar com o backend real)

- Confirmar nos microserviços reais o formato exato de cada payload
  (nomes de campos, uso de `id` vs `_id`) e ajustar `api.js`/páginas se
  necessário.
- Confirmar quais filtros (`titulo`, `cidade`, `modalidade`) a API de vagas
  suporta nativamente; o frontend já aplica um filtro extra no cliente como
  fallback.
- Avaliar CORS no backend para permitir requisições do domínio do frontend.
