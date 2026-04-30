# 🤖 ChatGPT Clone FullStack | Node.js + React + OpenAI API

![chadevpor](https://github.com/user-attachments/assets/e8c2ec72-7d82-4b13-aa72-8f28bcbdae7b)

---

## 📌 Visão Geral

Aplicação FullStack que simula a experiência do ChatGPT, integrando um front-end em React com um back-end em Node.js conectado à API oficial da OpenAI.

O projeto vai além de um clone visual: demonstra como construir, integrar e orquestrar sistemas baseados em IA para interação em tempo real.

---

## 🎯 Problema de Negócio

Interfaces de IA conversacional estão se tornando padrão em produtos digitais.

Porém, muitos desenvolvedores:
- Não sabem integrar corretamente APIs de IA em aplicações reais
- Limitam-se a exemplos isolados sem arquitetura completa
- Não entendem como transformar IA em produto funcional

---

## 🧠 Objetivo do Projeto

Construir uma aplicação completa que:

- Demonstra integração real com IA (OpenAI)
- Simula um produto moderno (chat conversacional)
- Apresenta arquitetura cliente-servidor escalável
- Permite customização de comportamento via engenharia de prompt

---

## 🧩 Contexto

A ascensão de aplicações baseadas em IA exige não apenas conhecimento de modelos, mas capacidade de:

- Integrar APIs externas
- Gerenciar estado de conversação
- Criar interfaces intuitivas
- Controlar comportamento do modelo

Este projeto foi desenvolvido como um laboratório prático dessas competências.

---

## ⚙️ Premissas

- A API da OpenAI é utilizada como motor de linguagem
- O histórico da conversa influencia as respostas
- O front-end simula experiência semelhante ao ChatGPT
- O sistema é stateless no back-end (sem banco de dados)

---

## 🧪 Estratégia da Solução

1. Construção de API REST com Node.js
2. Integração com OpenAI (chat completions)
3. Criação de interface React baseada em componentes
4. Gerenciamento de estado das conversas
5. Customização via prompt (modo especialista)
6. Comunicação cliente-servidor via HTTP

---

## 🏗️ Arquitetura 

chatgpt-clone/ ├── server/   → API Node.js (integração com OpenAI) └── web/      → Interface React (chat UI) 






---

## 💡 Funcionalidades

- Chat em tempo real com IA
- Histórico de mensagens
- Indicador de digitação
- Tema claro/escuro
- Interface inspirada no ChatGPT
- Modo "Especialista em Música" (engenharia de prompt)
- Responsividade (mobile e desktop)

---

## 🎯 Decisões Técnicas

**Node.js + Express**
- Simplicidade e baixo overhead para APIs REST
- Alta compatibilidade com integrações externas

**React**
- Componentização e reatividade
- Controle eficiente de estado

**OpenAI API**
- Abstração de modelos complexos de NLP
- Permite foco na aplicação, não no treinamento

**Sem banco de dados**
- Redução de complexidade
- Foco na experiência e integração

---

## 📊 Limitações

- Não há persistência em banco de dados
- Dependência da API externa (latência/custos)
- Não há controle avançado de contexto/token

---

## 📈 Possibilidades de Evolução

- Implementar persistência (MongoDB/PostgreSQL)
- Criar autenticação de usuários
- Adicionar streaming de resposta
- Implementar controle de contexto inteligente
- Deploy em cloud (Vercel + Render)

---

## 💻 Tecnologias Utilizadas

### Back-end
- Node.js
- Express
- OpenAI SDK
- dotenv
- CORS

### Front-end
- React
- Hooks (useState, useEffect)
- Fetch API
- CSS modular

---

## ▶️ Como Executar o Projeto

### Pré-requisitos
- Node.js v18+
- API Key da OpenAI

---

### 🔹 Back-end

```bash
cd server
npm install
echo "OPENAI_API_KEY=your_api_key_here" > .env
npm run dev
```

Servidor: http://localhost:5000




###  🔹 Front-end

cd web
npm install
npm start


Aplicação: http://localhost:3000⁠



---



## 🎼 Caso de Uso — Engenharia de Prompt

O modo "Especialista em Música" demonstra como direcionar o comportamento da IA via prompt.
Exemplo:

Explicação de escalas musicais
Sugestão de músicas por tonalidade
👉 Mostra que IA não é apenas consumo de API — é controle de comportamento.



---


## Aprendizados

• Integração real com APIs de IA

• Separação clara entre front-end e back-end

• Importância do estado em aplicações conversacionais

• Engenharia de prompt como diferencial estratégico

• Construção de aplicações orientadas a experiência


---


## Próximos Passos

• Deploy completo (produção)

• Logs e monitoramento

• Testes automatizados

• UX aprimorada

• Integração com múltiplos modelos




---

##  **Contato:**

[![Portfólio Sérgio Santos](https://img.shields.io/badge/Portfólio-Sérgio_Santos-111827?style=for-the-badge&logo=githubpages&logoColor=00eaff)](https://portfoliosantossergio.vercel.app)
[![LinkedIn Sérgio Santos](https://img.shields.io/badge/LinkedIn-Sérgio_Santos-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/santossergioluiz)



