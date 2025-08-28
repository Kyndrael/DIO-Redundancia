# 📝 Passo a Passo: Redundância de Arquivos com Azure Data Factory

Este guia explica como implementar o projeto de **redundância de arquivos** usando **Azure Data Factory**, conectando um banco **SQL on-premises** ao **Azure Data Lake (Blob Storage)**.

---

## 🔹 1. Preparar o Ambiente
1. Acesse o [Azure Portal](https://portal.azure.com).
2. Crie um **Resource Group** (ex.: `rg-datafactory-project`).
3. Crie os seguintes recursos:
   - **Azure Data Factory**
   - **Azure SQL Database** (se quiser simular o destino)
   - **Azure Blob Storage** (será usado como Data Lake)

---

## 🔹 2. Configurar o Integration Runtime
1. No **Azure Data Factory**, vá em **Manage > Integration Runtime**.
2. Crie um **Self-hosted Integration Runtime**.
3. Baixe e instale o agente no servidor local.
4. Registre a chave fornecida pelo ADF.

👉 Isso permite que o ADF acesse o banco de dados **on-premises**.

---

## 🔹 3. Criar os Linked Services
- **Linked Service - SQL On-Premises**
  - Tipo: `SqlServer`
  - Use o Integration Runtime Self-hosted
  - Informe a connection string e credenciais
- **Linked Service - Azure Blob Storage**
  - Tipo: `AzureBlobStorage`
  - Autenticação: chave da conta ou Managed Identity
- **Linked Service - Azure SQL Database** (opcional)

---

## 🔹 4. Criar os Datasets
- **Dataset Origem (SQL Server)**
  - Conectado ao Linked Service SQL On-Prem
  - Exemplo: tabela `dbo.Clientes`
- **Dataset Destino (Blob Storage)**
  - Tipo: `DelimitedText` (TXT/CSV)
  - Container: `datalake`
  - Caminho: `raw/clientes/`
  - Nome do arquivo dinâmico: `Clientes_@{pipeline().RunId}.txt`

---

## 🔹 5. Construir o Pipeline
1. Crie um novo Pipeline no ADF.
2. Adicione uma atividade **Copy Data**.
3. Configure:
   - Fonte: Dataset SQL
   - Destino: Dataset Blob
4. Ative a opção **First Row as Header** se necessário.
5. Teste a cópia no modo debug.

---

## 🔹 6. Publicar e Executar
1. Clique em **Validate All** para verificar erros.
2. Clique em **Publish All** para salvar.
3. Execute manualmente o pipeline.
4. Acompanhe em **Monitor > Pipeline Runs**.

---

## 🔹 7. Validar Resultados
- Verifique se o arquivo `.TXT` foi criado no **Blob Storage**.
- Confirme se os dados correspondem à tabela SQL de origem.
- Estrutura final esperada no Data Lake:

