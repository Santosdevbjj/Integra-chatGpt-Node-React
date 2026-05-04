# ChatGPT Clone FullStack | Node.js + React + OpenAI API

![chadevpor](https://github.com/user-attachments/assets/e8c2ec72-7d82-4b13-aa72-8f28bcbdae7b)

[![Portfólio Sérgio Santos](https://img.shields.io/badge/Portfólio-Sérgio_Santos-111827?style=for-the-badge&logo=githubpages&logoColor=00eaff)](https://portfoliosantossergio.vercel.app)
[![LinkedIn Sérgio Santos](https://img.shields.io/badge/LinkedIn-Sérgio_Santos-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/santossergioluiz)

---

## 1. Problema de Negócio

Interfaces de IA conversacional passaram de diferencial competitivo para expectativa básica de produto. Chatbots, assistentes internos e copilots corporativos são hoje demandas recorrentes em fintechs, healthtechs e govtechs — e a maioria dos times de desenvolvimento não sabe como construí-los corretamente.

O problema concreto tem três camadas:

- **Integração superficial:** a maioria dos exemplos disponíveis conecta diretamente o frontend à API da OpenAI, expondo a chave de API no cliente e sem nenhuma camada de controle de comportamento.
- **Ausência de arquitetura cliente-servidor:** aplicações reais precisam de um backend que gerencie o contexto da conversa, controle rate limits, aplique regras de negócio e proteja credenciais — o que exemplos de tutorial ignoram.
- **Desconhecimento de engenharia de prompt como camada de produto:** a maioria dos desenvolvedores trata o prompt como texto livre, sem perceber que ele é o mecanismo central de controle do comportamento da IA — e que isso é uma decisão de arquitetura, não de copywriting.

Este projeto existe para demonstrar como construir uma aplicação conversacional com IA de forma correta: com separação clara de responsabilidades, arquitetura cliente-servidor real e controle de comportamento via engenharia de prompt.

---

## 2. Contexto

Este projeto foi desenvolvido como laboratório prático de integração FullStack com IA generativa, combinando Node.js no backend com React no frontend, conectados à API oficial da OpenAI.

A motivação vai além do clone visual: em 15+ anos de sistemas bancários críticos no Bradesco, a separação entre camadas de aplicação é um princípio não negociável. Aplicações que expõem credenciais no cliente, que não gerenciam estado de sessão ou que não têm ponto de controle centralizado criam risco operacional e de segurança. O mesmo princípio se aplica a aplicações de IA — e a maioria dos tutoriais disponíveis viola todos esses critérios.

O caso de uso demonstrado — um modo "Especialista em Música" com engenharia de prompt customizada — foi escolhido por ser simples o suficiente para ser verificável (qualquer pessoa pode avaliar se as respostas sobre escalas musicais são corretas) e complexo o suficiente para demonstrar como o comportamento da IA muda com prompts de sistema bem estruturados.

---

## 3. Premissas

- A API da OpenAI é utilizada exclusivamente no backend — a chave de API nunca é exposta ao cliente.
- O histórico da conversa é gerenciado no estado do frontend e enviado a cada requisição, simulando o comportamento stateful sem banco de dados.
- O sistema é **stateless no backend por decisão de escopo**: sem persistência de sessão entre reinicializações do servidor, o que reduz a complexidade operacional para um projeto de demonstração.
- O comportamento do modelo é controlado via **system prompt** — a engenharia de prompt é tratada como configuração de produto, não como texto ad hoc.
- O modelo utilizado é `gpt-3.5-turbo` pela relação custo-performance para aplicações conversacionais sem necessidade de raciocínio avançado.

---

## 4. Estratégia da Solução

A arquitetura segue o padrão cliente-servidor com responsabilidades bem definidas em cada camada.

**Camada 1 — Backend: API REST com Node.js + Express**

O servidor expõe um único endpoint `POST /api/chat` que recebe o histórico de mensagens, encaminha para a OpenAI com o system prompt configurado e retorna a resposta do modelo. Essa camada centraliza:

- Autenticação com a OpenAI (chave de API via variável de ambiente)
- Configuração do modelo e parâmetros de geração
- Tratamento de erros e respostas de fallback
- CORS configurado para aceitar apenas origens autorizadas

```javascript
// Estrutura central do endpoint
app.post('/api/chat', async (req, res) => {
  const { messages } = req.body;
  const response = await openai.createChatCompletion({
    model: 'gpt-3.5-turbo',
    messages  // histórico completo enviado pelo cliente
  });
  res.json({ data: response.data.choices[0].message });
});
```

**Camada 2 — Frontend: Interface React com gerenciamento de estado**

A interface React gerencia o estado da conversa localmente via `useState`, renderiza as mensagens com distinção visual entre usuário e assistente, e envia o histórico completo a cada nova mensagem — o que permite ao modelo manter contexto conversacional sem banco de dados.

```javascript
// Gerenciamento de estado e envio com histórico acumulado
const handleSend = async (text) => {
  const newMsg = { role: 'user', content: text };
  const updated = [...messages, newMsg];
  setMessages(updated);
  const response = await fetch('http://localhost:5000/api/chat', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ messages: updated }),
  });
  const { data } = await response.json();
  setMessages([...updated, data]);
};
```

**Camada 3 — Engenharia de Prompt: controle de comportamento via system prompt**

O modo "Especialista em Música" demonstra como um system prompt bem estruturado transforma o comportamento do modelo. O mesmo endpoint, com um prompt de sistema diferente, produz respostas sobre escalas musicais, sugestões por tonalidade e teoria musical — sem nenhuma alteração no código da aplicação.

**Estrutura de componentes (Frontend)**

```
src/
├── components/
│   ├── Chat.js       → Lista de mensagens + campo de input
│   ├── Message.js    → Renderização de cada mensagem (usuário / bot)
│   ├── Sidebar.js    → Menu lateral, temas, opções
│   └── CSS/
│       └── App.css   → Layout, cores, responsividade
└── App.js            → Orquestração de estado e comunicação com backend
```

---

## 5. Insights Técnicos

**O histórico acumulado é o mecanismo de "memória" do modelo**

A OpenAI não mantém estado entre chamadas. A "memória" da conversa é simulada enviando o array completo de mensagens anteriores a cada nova requisição. Isso tem uma implicação importante: o custo por requisição aumenta linearmente com o tamanho da conversa, porque cada chamada inclui todos os tokens do histórico. Em produção, esse comportamento exige uma estratégia de janela deslizante ou sumarização automática do contexto para controle de custos.

**System prompt determina a "persona" do produto — não o código**

O modo "Especialista em Música" prova um ponto arquitetural relevante: a diferença entre um assistente genérico e um produto especializado está inteiramente no system prompt. Isso significa que a camada de engenharia de prompt é, na prática, a camada de produto da aplicação — e merece o mesmo rigor de versionamento e teste que o código.

**CORS no backend é uma decisão de segurança, não de conveniência**

Configurar CORS corretamente no Express — restringindo origens permitidas em produção — é o primeiro passo para evitar que terceiros façam chamadas ao seu backend usando sua chave de API. Em desenvolvimento, `cors()` sem configuração aceita qualquer origem. Em produção, isso deve ser `cors({ origin: 'https://seu-dominio.com' })`.

**Ausência de banco de dados é uma decisão válida — dentro do escopo correto**

A escolha de não persistir conversas elimina autenticação, migrações, backup e toda a complexidade operacional associada. Para um projeto de demonstração técnica, essa é a decisão certa. O custo é que o histórico desaparece ao recarregar a página — o que, documentado explicitamente, é transparência técnica, não limitação escondida.

---

## 6. Resultados

A aplicação entrega uma interface conversacional funcional com as seguintes características verificáveis:

| Funcionalidade | Implementação | Status |
|---|---|---|
| Chat em tempo real com IA | OpenAI `gpt-3.5-turbo` via backend | ✅ |
| Histórico de mensagens por sessão | Estado React acumulado | ✅ |
| Proteção da chave de API | Credencial exclusivamente no backend (.env) | ✅ |
| Modo "Especialista em Música" | System prompt configurável | ✅ |
| Distinção visual usuário / assistente | Componente `Message.js` com avatares | ✅ |
| Tema claro/escuro | CSS modular + Sidebar | ✅ |
| Responsividade mobile/desktop | CSS flexbox/grid | ✅ |
| Tratamento de erros | Try/catch no backend + feedback ao usuário | ✅ |

O caso de uso de escalas musicais demonstra o funcionamento do modo especialista: o modelo responde corretamente sobre padrões de tons e semitons (T-T-S-T-T-T-S), escalas maiores (Dó, Sol, Fá, Ré, Mi, Si) e sugestões de músicas por tonalidade — comportamento que não existiria sem o system prompt configurado.

---

## 7. Decisões Técnicas

**Por que Node.js + Express e não NestJS ou Fastify?**

Para um backend com responsabilidade única (um endpoint de proxy para a OpenAI), o overhead de um framework mais estruturado não se justifica. Express oferece o mínimo necessário: roteamento, middleware de CORS e parsing de JSON. Se o backend crescer para incluir autenticação, múltiplos modelos e persistência, NestJS seria a escolha natural — pela estrutura de módulos e injeção de dependência que facilitam testes.

**Por que React com state local e não Redux ou Zustand?**

O estado da conversa tem uma única fonte de verdade (o array de mensagens) e um único ponto de mutação (a função `handleSend`). Introduzir uma biblioteca de gerenciamento de estado para esse escopo seria over-engineering — o mesmo erro que colocar Kubernetes em um projeto com um único serviço.

**Por que não streaming de resposta?**

A API da OpenAI suporta streaming via Server-Sent Events, o que permitiria exibir tokens conforme são gerados — como o ChatGPT real faz. A decisão de não implementar foi de escopo: streaming exigiria refatorar o backend para usar `res.write()` em vez de `res.json()` e o frontend para consumir um stream, o que aumentaria a complexidade sem adicionar valor à demonstração dos conceitos centrais. Está listado nos próximos passos com prioridade.

**Por que `gpt-3.5-turbo` e não `gpt-4`?**

Custo por token. Para uma aplicação de demonstração sem requisito de raciocínio avançado, `gpt-3.5-turbo` tem performance adequada a aproximadamente 10x menos custo. Em produção com caso de uso real, a escolha do modelo dependeria do tipo de tarefa — raciocínio complexo ou code generation favoreceriam GPT-4.

---

## 8. Aprendizados

**O que funcionou bem:**

A separação física entre `/server` e `/web` forçou a pensar na fronteira entre as camadas desde o início. Quando frontend e backend estão no mesmo projeto, a tentação de misturar responsabilidades é constante — a estrutura de pastas separadas é uma barreira de design que ajuda a manter a arquitetura limpa.

**O que faria diferente:**

Implementaria streaming de resposta desde o início — a experiência do usuário é significativamente melhor com tokens aparecendo progressivamente do que com a espera pela resposta completa. Também adicionaria um rate limiter no backend (via `express-rate-limit`) para evitar abuso da chave de API em caso de deploy público.

**Aprendizado central:**

Engenharia de prompt não é uma habilidade de escrita — é uma habilidade de arquitetura. O system prompt define o contrato de comportamento da IA com o produto. Versionar, testar e documentar esse prompt com o mesmo rigor que o código é o que separa uma integração amadora de um produto de IA confiável.

---

## 9. Próximos Passos

- [ ] Implementar **streaming de resposta** via Server-Sent Events (SSE) para experiência token a token
- [ ] Adicionar **rate limiter** no backend com `express-rate-limit` para proteção da API key em produção
- [ ] Implementar **persistência de conversas** com MongoDB ou PostgreSQL + autenticação JWT
- [ ] Adicionar **janela deslizante de contexto** para controle de tokens e custos em conversas longas
- [ ] **Deploy completo**: frontend no Vercel + backend no Render com variáveis de ambiente seguras
- [ ] Adicionar **testes automatizados** no backend com Jest + Supertest para o endpoint `/api/chat`
- [ ] Implementar **múltiplos modos de especialista** configuráveis via interface (não hardcoded)
- [ ] Integrar **monitoramento de custos** por sessão via OpenAI Usage API

---

## Como Executar o Projeto

**Pré-requisitos:** Node.js v18+ e uma API Key da OpenAI.

**Backend**

```bash
cd server
npm install
echo "OPENAI_API_KEY=your_api_key_here" > .env
npm run dev
# Servidor disponível em http://localhost:5000
```

**Frontend**

```bash
cd web
npm install
npm start
# Aplicação disponível em http://localhost:3000
```

> ⚠️ O frontend espera o backend rodando em `localhost:5000`. Inicie o backend antes do frontend.

---

## Estrutura do Repositório

```
📁 Integra-chatGpt-Node-React/
├── README.md
├── server/
│   ├── index.js          # API REST — endpoint /api/chat + configuração OpenAI
│   ├── package.json
│   └── .env              # OPENAI_API_KEY (não versionar — adicionar ao .gitignore)
└── web/
    ├── src/
    │   ├── App.js         # Orquestração de estado e comunicação com backend
    │   └── components/
    │       ├── Chat.js    # Lista de mensagens + campo de input
    │       ├── Message.js # Renderização por role (user / assistant)
    │       ├── Sidebar.js # Menu lateral, temas, opções
    │       └── CSS/
    │           └── App.css
    └── package.json
```

---

## Stack Tecnológica

| Camada | Tecnologia | Decisão |
|---|---|---|
| Backend | Node.js + Express | Baixo overhead para API de proxy single-endpoint |
| Integração IA | OpenAI SDK (`gpt-3.5-turbo`) | Custo-performance para conversação |
| Credenciais | dotenv | API key exclusivamente no servidor |
| CORS | `cors` middleware | Controle de origens autorizadas |
| Frontend | React (CRA) | Componentização + gerenciamento de estado local |
| Estado | useState / useEffect | Escopo simples — sem necessidade de Redux |
| HTTP Client | Fetch API nativa | Sem dependência externa para requisições |
| Estilização | CSS modular | Responsividade sem framework |

---

## Documentação Complementar

- [`server.md`](./chatgpt-clone/server.md) — Setup completo do backend: inicialização, `index.js` anotado e configuração `.env`
- [`web.md`](./chatgpt-clone/web/web.md) — Setup do frontend: estrutura de componentes, `App.js` anotado e estilização
- [`comoRodar.md`](./comoRodar.md) — Comandos de execução resumidos
- [`escalasMusicais.md`](./escalasMusicais.md) — Caso de teste do modo "Especialista em Música": escalas e sugestões por tonalidade

---

*Projeto desenvolvido por **Sérgio Santos** — Data Engineer & Cloud Architect com 15+ anos em sistemas bancários críticos.*  
*Campus Expert #14 na [DIO](https://www.dio.me/).*
