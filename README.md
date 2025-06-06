# AsyncPDF



### Estrutura de Pastas e Arquivos (Exemplo)

```
pdf_processor_app/
├── app.py
├── config.py
├── requirements.txt
├── .env
├── .gitignore
├── README.md
├── instance/
│   └── config.py  (para configurações específicas da instância, não versionadas)
├── migrations/
│   ├── alembic.ini
│   └── versions/
│       └── <timestamp>_initial_migration.py
├── src/
│   ├── __init__.py
│   ├── models.py
│   ├── schemas.py
│   ├── services/
│   │   ├── __init__.py
│   │   ├── pdf_service.py
│   │   └── queue_service.py
│   ├── routes/
│   │   ├── __init__.py
│   │   └── pdf_routes.py
│   └── tasks/
│       ├── __init__.py
│       └── pdf_tasks.py
├── static/
│   └── css/
│   └── js/
│   └── images/
├── templates/
│   └── index.html
│   └── base.html
├── tests/
│   ├── __init__.py
│   ├── test_pdf_processing.py
│   └── test_api_endpoints.py
├── worker.py  (para o worker Celery ou RQ)
└── docker-compose.yml
```

### Explicação Detalhada das Pastas e Arquivos:

**Diretório Raiz (`pdf_processor_app/`)**

* **`app.py`** :
* **Função** : O ponto de entrada principal da aplicação Flask.
* **Conteúdo** : Inicializa a aplicação Flask, configura extensões (como SQLAlchemy, Marshmallow, Celery/RQ), registra blueprints e define as rotas básicas.
* **`config.py`** :
* **Função** : Contém as configurações globais da aplicação.
* **Conteúdo** : Variáveis de ambiente padrão, configurações do banco de dados, chaves secretas (para desenvolvimento/padrão), configurações do Celery/RQ, etc. É uma boa prática ter configurações separadas para diferentes ambientes (desenvolvimento, teste, produção).
* **`requirements.txt`** :
* **Função** : Lista de todas as dependências Python do projeto.
* **Conteúdo** : `Flask`, `Flask-SQLAlchemy`, `Flask-Migrate`, `marshmallow`, `celery` (ou `rq`), `redis`, `pypdf`, `reportlab`, etc.
* **`.env`** :
* **Função** : Armazena variáveis de ambiente sensíveis e específicas para o ambiente de desenvolvimento local.
* **Conteúdo** : Chaves de API, senhas de banco de dados, URLs de serviços externos.  **Este arquivo deve ser adicionado ao `.gitignore`** .
* **`.gitignore`** :
* **Função** : Especifica quais arquivos e pastas o Git deve ignorar.
* **Conteúdo** : `.env`, `__pycache__/`, `instance/`, `venv/`, logs, etc.
* **`README.md`** :
* **Função** : Documentação do projeto.
* **Conteúdo** : Descrição do projeto, como configurar o ambiente, como executar a aplicação, como usar a API, etc.

**Pastas Internas**

* **`instance/`** :
* **Função** : Contém configurações específicas da instância que não devem ser versionadas.
* **Conteúdo** :
  *  **`config.py`** : Pode sobrescrever configurações do `config.py` principal, por exemplo, para definir um banco de dados local ou chaves de API específicas para aquela instalação.  **Este arquivo deve ser adicionado ao `.gitignore`** .
* **`migrations/`** :
* **Função** : Gerenciamento de migrações de banco de dados (usando `Flask-Migrate` e `Alembic`).
* **Conteúdo** :
  *  **`alembic.ini`** : Arquivo de configuração do Alembic.
  *  **`versions/`** : Pasta que armazena os scripts de migração gerados, cada um com um timestamp e nome descritivo (ex: `<timestamp>_initial_migration.py`).
* **`src/`** :
* **Função** : Onde a lógica principal da aplicação reside. É o coração da aplicação.
* **Conteúdo** :
  *  **`__init__.py`** : Geralmente vazio ou para configurações de pacote.
  *  **`models.py`** :
  * **Função** : Define os modelos de dados do banco de dados (usando SQLAlchemy).
  * **Conteúdo** : Classes para representar tarefas de PDF, usuários, status de processamento, etc. (ex: `Task`, `User`).
    *  **`schemas.py`** :
  * **Função** : Define os esquemas de serialização/desserialização de dados (usando Marshmallow ou Pydantic).
  * **Conteúdo** : Esquemáticos para validação de entrada da API e formatação de saída (ex: `TaskSchema`, `PDFUploadSchema`).
    *  **`services/`** :
  * **Função** : Contém a lógica de negócio complexa que não pertence diretamente às rotas ou aos modelos.
  * **Conteúdo** :
  * **`__init__.py`** :
  * **`pdf_service.py`** : Lógica para manipulação real de PDF (dividir, unir, adicionar marca d'água, etc.). Pode usar bibliotecas como `pypdf`, `reportlab`.
  * **`queue_service.py`** : Lógica para interagir com a fila de tarefas (Celery ou RQ), enfileirando e consultando o status das tarefas.
    *  **`routes/`** :
  * **Função** : Define as rotas da API (usando Blueprints do Flask).
  * **Conteúdo** :
  * **`__init__.py`** :
  * **`pdf_routes.py`** : Define endpoints para upload de PDF, consulta de status de tarefas, download de resultados, etc. (ex: `/upload`, `/tasks/<task_id>`).
    *  **`tasks/`** :
  * **Função** : Define as tarefas assíncronas que serão executadas pelos workers.
  * **Conteúdo** :
  * **`__init__.py`** :
  * **`pdf_tasks.py`** : Funções decoradas com `@celery.task` (ou similar para RQ) que realizam o processamento de PDF, como `process_pdf_task(file_path, operation_type, task_id)`.
* **`static/`** :
* **Função** : Contém arquivos estáticos servidos diretamente pelo Flask (CSS, JavaScript, Imagens).
* **Conteúdo** :
  *  **`css/`** : Arquivos CSS.
  *  **`js/`** : Arquivos JavaScript.
  *  **`images/`** : Imagens.
* **`templates/`** :
* **Função** : Contém os arquivos de template Jinja2 para renderização de HTML.
* **Conteúdo** :
  *  **`index.html`** : Página principal da aplicação (se houver uma interface web).
  *  **`base.html`** : Template base para outras páginas.
* **`tests/`** :
* **Função** : Contém os testes automatizados da aplicação.
* **Conteúdo** :
  *  **`__init__.py`** :
  *  **`test_pdf_processing.py`** : Testes unitários e de integração para a lógica de processamento de PDF.
  *  **`test_api_endpoints.py`** : Testes para os endpoints da API.
* **`worker.py`** :
* **Função** : O script que inicializa e executa o worker da fila de tarefas (Celery Worker ou RQ Worker).
* **Conteúdo** : Configura o Celery/RQ e inicia o worker para processar as tarefas enfileiradas.
* **`docker-compose.yml`** :
* **Função** : Define e executa a aplicação usando Docker Compose.
* **Conteúdo** : Configura serviços como a aplicação Flask, um servidor Redis (para a fila de tarefas e cache) e um banco de dados (PostgreSQL ou MySQL).

### Fluxo Típico de Operação

1. **Requisição API (Frontend/Cliente)** : Um cliente envia uma requisição POST para a API (ex: `/upload`) com um arquivo PDF e as operações desejadas.
2. **Rota Flask (`pdf_routes.py`)** : A rota recebe a requisição, valida os dados usando um `schema.py`, salva o arquivo PDF temporariamente e cria uma nova tarefa no banco de dados (`models.py`) com o status "PENDING".
3. **Enfileiramento (`queue_service.py`)** : A rota, ou um serviço auxiliar, chama o `queue_service` para enfileirar a tarefa de processamento de PDF no Celery/RQ, passando o ID da tarefa e o caminho do arquivo temporário.
4. **Worker (`worker.py` e `pdf_tasks.py`)** : O worker da fila de tarefas pega a tarefa da fila. A função `process_pdf_task` em `pdf_tasks.py` é executada.
5. **Processamento de PDF (`pdf_service.py`)** : Dentro da `process_pdf_task`, o `pdf_service.py` é chamado para realizar as operações reais no PDF (dividir, unir, etc.).
6. **Atualização de Status (`models.py`)** : Após o processamento, a tarefa no banco de dados é atualizada para "COMPLETED" ou "FAILED", e o caminho para o PDF resultante é armazenado.
7. **Resposta API (Frontend/Cliente)** : O cliente pode polling a rota `/tasks/<task_id>` para verificar o status da tarefa. Uma vez concluída, ele pode baixar o PDF processado.

### Ferramentas e Bibliotecas Essenciais

* **Flask** : Microframework web para Python.
* **Flask-SQLAlchemy** : Extensão para integração com SQLAlchemy (ORM).
* **Flask-Migrate** : Para gerenciar migrações de banco de dados com Alembic.
* **Marshmallow (ou Pydantic)** : Para serialização/desserialização e validação de dados.
* **Celery (ou RQ)** : Para gerenciar filas de tarefas assíncronas. Celery é mais robusto e tem mais recursos, RQ é mais simples e pode ser uma boa opção para começar.
* **Redis** : Como broker para Celery/RQ e/ou para caching.
* **PostgreSQL (ou outro banco de dados)** : Para armazenar informações sobre as tarefas, usuários, etc.
* **pypdf (ou PyPDF2, ou pikepdf)** : Para manipulação de PDF (dividir, unir, extrair texto, etc.).
* **ReportLab** : Se precisar gerar PDFs programaticamente (adicionar marcas d'água complexas, etc.).
* **Gunicorn/uWSGI** : Para servir a aplicação Flask em produção.
* **Docker/Docker Compose** : Para facilitar a orquestração de containers e o ambiente de desenvolvimento/produção.
