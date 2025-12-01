# NLW Agents

> Projeto de estudo desenvolvido e aprendido durante o evento NLW Agents da Rocketseat.

Tratava-se de um projeto Fullstack; contudo, infelizmente perdi o frontend.

Resumo
- API backend que organiza "salas" (rooms), aceita upload de áudios, gera transcrições e embeddings, e responde perguntas usando similaridade vetorial e o modelo Gemini.
- Objetivo didático: demonstrar integração entre Fastify, Drizzle ORM, pgvector e um modelo generativo para casos de uso real.

Tecnologias utilizadas
1. IDE
   - Visual Studio Code (configurações em [.vscode/settings.json](.vscode/settings.json))
2. Bibliotecas / Ferramentas principais
   - [fastify](package.json) — servidor HTTP (ver [src/server.ts](src/server.ts))
   - [drizzle-orm](package.json) — ORM type-safe (esquemas em [src/db/schema/index.ts](src/db/schema/index.ts))
   - [postgres](package.json) + [pgvector extension](docker/setup.sql) — banco com suporte a vetores (config em [docker-compose.yml](docker-compose.yml))
   - [@google/genai](package.json) — integração com Gemini (implementação em [src/services/gemini.ts](src/services/gemini.ts))
   - [zod](package.json) + [fastify-type-provider-zod](package.json) — validação de request schemas (ex.: rotas em [src/http/routes](src/http/routes))
   - Auxiliares: [drizzle-kit](package.json), [drizzle-seed](package.json)
3. Métodos (funções-chave)
   - [`transcribeAudio`](src/services/gemini.ts) — envia áudio para transcrição
   - [`generateEmbeddings`](src/services/gemini.ts) — cria embeddings para textos
   - [`generateAnswer`](src/services/gemini.ts) — gera resposta baseada em contexto recuperado
4. Metodologias
   - Arquitetura modular (separação entre rotas, serviços e schema)
   - Validação de entrada com Zod
   - Migrations e seed com Drizzle
   - Uso de vetores para recuperação semântica (pgvector)

Resumo da proposta do projeto
- Receber áudios por rota (ex.: [src/http/routes/upload-audio.ts](src/http/routes/upload-audio.ts)), transcrever e salvar chunks com embeddings ([src/db/schema/audio-chunks.ts](src/db/schema/audio-chunks.ts)).
- Ao receber uma pergunta ([src/http/routes/create-question.ts](src/http/routes/create-question.ts)), gerar embeddings da pergunta, buscar chunks semelhantes no mesmo room e usar Gemini para construir uma resposta baseada apenas no contexto recuperado.
- Expor endpoints para criar/listar salas e listar perguntas por sala (ver [src/http/routes/get-rooms.ts](src/http/routes/get-rooms.ts) e [src/http/routes/get-room-questions.ts](src/http/routes/get-room-questions.ts)).

Requisitos do projeto
- Docker e Docker Compose (para rodar PostgreSQL com pgvector) — arquivo [docker-compose.yml](docker-compose.yml)
- Node.js (versão compatível com --experimental-strip-types se usar scripts do package.json)
- Variáveis de ambiente em `.env`:
  - PORT (opcional; padrão 3333)
  - DATABASE_URL (ex.: postgresql://user:pass@host:5432/db)
  - GEMINI_API_KEY
  - Ex.: veja [src/env.ts](src/env.ts)
- Comandos úteis:
  - Instalar deps:
    ```sh
    npm install
    ```
  - Subir DB em Docker:
    ```sh
    docker-compose up -d
    ```
  - Rodar migrações:
    ```sh
    npx drizzle-kit migrate
    ```
  - Popular base (opcional):
    ```sh
    npm run db:seed
    ```
  - Iniciar servidor (desenvolvimento):
    ```sh
    npm run dev
    ```

Referências e observações
- Migrations e snapshots em [src/db/migrations](src/db/migrations)
- Conexão e configuração do Drizzle em [src/db/connection.ts](src/db/connection.ts) e [drizzle.config.ts](drizzle.config.ts)
- Extensão de vetor ativada no startup do Docker por [docker/setup.sql](docker/setup.sql)
- Projeto criado como estudo no NLW Agents da Rocketseat — finalidade educacional; o nome do repositório/estrutura pode refletir o exercício do evento.

Pessoas desenvolvedoras
- Gabrielly mossa

Arquivos importantes (leitura recomendada)
- [src/server.ts](src/server.ts)
- [src/services/gemini.ts](src/services/gemini.ts) — transcrição / embeddings / geração de resposta
- [src/http/routes/create-question.ts](src/http/routes/create-question.ts)
- [src/http/routes/upload-audio.ts](src/http/routes/upload-audio.ts)
- [src/db/schema/audio-chunks.ts](src/db/schema/audio-chunks.ts)
- [package.json](package.json)
- [docker-compose.yml](docker-compose.yml)

