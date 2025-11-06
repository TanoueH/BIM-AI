# BIM-AI

%% ------------------------------------------------------------
%% 🧩 ARQUITETURA DE RETROFIT INTELIGENTE (Mermaid - Markdown)
%% ------------------------------------------------------------
flowchart TD
    %% Section: BIM
    A1([🏗️ Revit Model<br>(RVT / IFC)]):::bim -->|Exportar Quantitativos| A2([💻 pyRevit<br>Gatilho Inicial])
    A2 -->|PUT IFC/CSV/JSON| B1([🗄️ MinIO<br>Object Store (S3)])
    A2 -->|Evento REST| B2([🔄 NiFi<br>Orquestrador de Ingestão])
    
    %% Section: NiFi Process
    B2 -->|Valida & Enriquecer| B3([📄 Schema Validation<br>+ SINAPI Mapping])
    B3 -->|Transforma CSV→JSON| B4([🧩 Grava em Postgres<br>tabelas: quantitativos, logs])
    B4 -->|Publica evento| C1([📬 TaskQueue<br>(Redis / RabbitMQ)])
    
    %% Section: AI Orchestration
    C1 -->|Plano de ação| D1([🧠 GPT-5<br>Orquestrador / Gerente])
    D1 -->|Injeta tarefas| C1
    C1 -->|Consumir tarefas| D2([🤖 Mistral Agents<br>(via Ollama)])
    D2 -->|Resultados JSON + Planilhas| B1
    D2 -->|Persistir resultados| B4
    D2 -->|Métricas Execução| G1([📊 Prometheus + Grafana])
    
    %% Section: Validation + Reporting
    D1 -->|Valida resultados| D3([📝 Relatório Técnico<br>(PDF / HTML)])
    D3 -->|Armazena versão| B1
    D3 -->|Cria tarefa PM| E1([📋 ProjectNext.AI<br>Gestão de Projetos])
    
    %% Section: Visualization
    B4 -->|Consulta BI| F1([📈 Superset<br>Relatórios Financeiros])
    G1 -->|Dashboards Operacionais| F2([📉 Grafana<br>Monitoração de Jobs])
    
    %% Styles
    classDef bim fill:#1D90F5,stroke:#004C8C,color:#fff;
    classDef infra fill:#2E2E2E,stroke:#999,color:#fff;
    classDef data fill:#5B8FF9,stroke:#003366,color:#fff;
    classDef ai fill:#915EFF,stroke:#4A148C,color:#fff;
    classDef bi fill:#00A86B,stroke:#004D26,color:#fff;

    class A1,A2 bim;
    class B1,B2,B3,B4 data;
    class C1 infra;
    class D1,D2,D3 ai;
    class E1,F1,F2,G1 bi;
