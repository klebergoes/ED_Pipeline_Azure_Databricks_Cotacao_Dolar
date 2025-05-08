# Projeto de Engenharia de Dados - Dólar comercial (Venda e Compra) - Cotações Diárias

## Sumário

[Desafio / Problema](#Desafio-/-Problema)

[Tecnologias Utilizadas](#Tecnologias-Utilizadas)

[Estrutura do Projeto](#Estrutura-do-Projeto)

[Conjunto de Dados](#Conjunto-de-Dados)

[Metodologia Pipeline](#Metodologia-Pipeline)

[Como Executar](#Como-Executar)

[Resultados](#Resultados)

[Conclusão](#Conclusão)

[Contato](#Contato)


## Desafio / Problema

Uma renomada empresa com sede nos Estados Unidos possui filiais no Brasil. Essas unidades realizam compras e vendas em sua respectiva moeda local (BRL). Para consolidar as informações financeiras, todas as transações precisam ser convertidas para dólares americanos (USD), garantindo uma visão unificada de receita, custo e lucro por país.

A área de negócios depende diariamente dessas cotações para gerar relatórios financeiros consolidados. No entanto, esse processo ainda é realizado de forma manual, sujeito a erros e atrasos. Para automatizar essa operação, será desenvolvido um pipeline de dados que consome cotações cambiais por meio da API do Banco Central, trata e padroniza as informações, entregando-as em um formato pronto para consumo.


## Tecnologias Utilizadas

-   **Linguagem:** Python (com uso de PySpark para processamento distribuído).
  
-   **Serviços e Ferramentas:** Azure Data Factory, Azure Data Lake Storage Gen2 e Databricks.


## Estrutura do Projeto

-   **Arquitetura_Medalhao:** Notebooks (.ipynb) com o desenvolvimento das camadas Silver e Gold da arquitetura em camadas (Medalhão).
  
-   **Factory-Databricks:** Arquivos de configuração (JSON) e templates usados em processos de automação e implantação de recursos no Azure, especialmente para Azure Data Factory e Azure Databricks.

-   **Dataset:** Arquivos de configuração (JSON) que definem a estrutura dos dados e o local de origem e destino para operações de leitura e gravação.

-   **Factory:** Arquivo de configuração (JSON) relacionado a autenticação e identidade de recursos.
  
-   **LinkedService:** Arquivos de configuração (JSON) para conectar o Data Factory a fontes de dados externas ou destinos, permitindo realizar operações de movimentação de dados.
  
-   **Pipeline:** Arquivo de configuração (JSON) que descrevem o pipeline no Azure Data Factory.
  
-   **Trigger:** Arquivo de configuração (JSON) que descrevem o trigger (gatilho) para o pipeline no Azure Data Factory.
  
-   **.gitignore:** Arquivo que contém o nome do notebook (.ipynb) de configuração do ponto de montagem (para o Databricks acessar o Azure Data Lake Storage Gen2), a ser ignorado durante o processo de versionamento. Por ser confidencial, não é incluído no repositório.
  
-   **README.md:** Documentação do projeto.
  
-   **Publish_config.json:** Arquivo de configuração (JSON) do Databricks com o sistema de versionamento de código.


## Conjunto de Dados

-   **Fonte:** API PTAX (Ferramenta oficial do Banco Central do Brasil).
  
-   **Endpoint:** "https://olinda.bcb.gov.br/olinda/servico/PTAX/versao/v1/odata/CotacaoDolarPeriodo(dataInicial=@dataInicial,dataFinalCotacao=@dataFinalCotacao)?%40dataInicial='01-01-2020'&%40dataFinalCotacao='12-31-2030'&%24format=text%2Fcsv".
  
-   **Retorno:** Cotação de Compra e a Cotação de Venda da moeda Dólar contra a unidade monetária corrente para o período informado.
  
-   **Formato:** CSV via API.
  
-   **Colunas:**
  
    -   **cotacaoCompra:** Cotação de compra do dólar contra a unidade monetária corrente: unidade monetária corrente/dólar americano.
      
        -   **Tipo de Dado:** decimal.
          
    -   **cotacaoVenda:** Cotação de venda do dólar contra a unidade monetária corrente: unidade monetária corrente/dólar americano.
      
        -   **Tipo de Dado:** decimal.
          
    -   **dataHoraCotacao:** Data, hora e minuto das cotações de compra e venda.
      
        -   **Tipo de Dado:** texto.


## Metodologia Pipeline

Para atender a esse objetivo, será adotada uma arquitetura de dados moderna baseada em camadas Bronze (Raw / Dados Brutos), Silver (Dados Limpos / Estruturados) e Gold (Dados Agregados / Prontos para Consumo), utilizando serviços em nuvem da Microsoft Azure. A solução contará com o Databricks como principal motor de processamento e transformação de dados, garantindo escalabilidade, governança e eficiência em todo o fluxo de dados:

![Image](https://github.com/user-attachments/assets/06c1c884-d9c9-4548-b0eb-d4dc841a9c31)

#### Detalhamento Pipeline:

- **Ingestão de dados - API -> Camada Bronze**

    - **Serviço Azure Data Factory**
  
        - **Componentes:**
      
            - **Trigger:** Inicia chamada a API as 0:00
          
            - **Linked Service:**
          
                - **Origem:**  Conecta à API (HttpServer)
              
                - **Destino:** Conecta ao Azure Data Lake Storage Gen2
          
            - **Dataset:**
          
                - **Csv_Api:** define a origem (formato CSV da API)
              
                - **Parquet_Datalake:** define o destino (formato Parquet no Data Lake)
          
            - **Activity - Copy Data:** Entre os datasets Csv_Api para Parquet_Datalake
              
- **Transformação - Camada Bronze -> Camada Silver**

    - **Serviço Databricks**
  
        - **Componentes:**
      
            - **Notebook:** Script PySpark para transformar os dados da Camada Bronze para Silver
          
            - **Cluster:** Configuração (ex: 16 GB RAM, 4 Cores)
  
    - **Serviço Azure Data Factory**
  
        - **Componentes:**
      
            - **Linked Service:** Conecta o ADF ao Databricks
          
            - **Activity - Notebook:** Executa o notebook “1. Camada Silver”
              
- **Modelagem Analítica - Camada Silver → Camada Gold**

    - **Serviço: Databricks**
  
        - **Componentes:**
      
            - **Notebook:** Script PySpark para transformar os dados da Camada Silver para Gold
          
            - **Cluster:** Cluster: Configuração (ex: 16 GB RAM, 4 Cores)
  
    - **Serviço Azure Data Factory**
  
        - **Componentes:**
      
            - **Linked Service:** Conecta o Azure Data Factory ao Databricks
          
            - **Activity - Notebook:** Executa o notebook “2. Camada Gold”

#### Fluxograma Azure Data Factory:

![Image](https://github.com/user-attachments/assets/8415dce5-61ef-46cb-9db3-4e18d6bc06fc)

#### Tempo de execução do pipeline:

![Image](https://github.com/user-attachments/assets/d97d68b8-678f-4cb9-ab3e-530261475abb)


## Como Executar

Para executar o projeto disponível no GitHub, que contém arquivos JSON do Azure Data Factory e notebooks do Databricks, siga as etapas abaixo para importar, configurar e executar ambos os componentes em seus respectivos serviços:

- **Azure Data Factory (Importar o projeto JSON):**
  
    - Acesse o Azure Data Factory Studio.
      
    - Vá até o menu Manage > Git configuration.
      
    - Preencha os campos da seguinte forma:
      
        - Tipo de repositório: GitHub
          
        - Proprietário do repositório do GitHub: klebergoes
          
        - Nome do repositório: ED_Pipeline_Azure_Databricks_Cotacao_Dolar
          
    - Após a configuração, você poderá navegar pelos arquivos diretamente no painel Factory Resources.

- **Databricks (Importar notebooks):**
  
    - Acesse o menu lateral no Databricks e clique em Repos.
      
    - Clique em Add Repo.
      
    - Preencha os campos da seguinte forma:
      
        - Git repository URL: https://github.com/klebergoes/ED_Pipeline_Azure_Databricks_Cotacao_Dolar.git
 
        - Git provider: GitHub
          
        - Repository name: ED_Pipeline_Azure_Databricks_Cotacao_Dolar
          
    - Após a importação, os notebooks estarão disponíveis no seu workspace para execução.
    
- **Integração: Azure Data Factory x Databricks**
  
    - Certifique-se de que o Linked Service do ADF para Databricks está configurado.
      
    - Confirme o nome do notebook e o caminho.
      
    - Teste a execução do pipeline no ADF.
 
- **Dica final: Teste por partes**
  
    - Rode o notebook isoladamente primeiro no Databricks.
      
    - Depois execute os pipelines no ADF.
      
    - Isso facilita a depuração de erros.


## Resultados

- **Redução de Erros Manuais:**
  
    - Elimina tarefas repetitivas e sujeitas a falha humana.
      
    - Garante consistência na execução de processos.
      
- **Escalabilidade na nuvem:**
  
    - Tarefas que levavam horas podem ser executadas em minutos ou segundos.
      
    - Permite execução em horários não comerciais (ex: agendamentos noturnos).

- **Aumento de Produtividade:**
  
    -  Libera os profissionais para se concentrarem em tarefas analíticas e estratégicas.
      
    -  Processos que antes exigiam esforço humano passam a rodar automaticamente.
      
-  **Melhor Monitoramento e Controle:**
  
    - Registros de execução (logs), alertas e dashboards facilitam auditoria e rastreabilidade.
  
    - Possibilidade de identificar gargalos e oportunidades de melhoria.
 
-  **Escalabilidade:**
  
    -  O mesmo fluxo automatizado pode ser replicado para múltiplos arquivos, bases, clientes ou regiões com pouca ou nenhuma alteração.
 
-  **Redução de Custos Operacionais:**
  
    -  Menor dependência de trabalho manual e tempo gasto com retrabalho.
      
    -  Uso inteligente de recursos computacionais (como clusters sob demanda, por exemplo no Databricks).
 
-  **Agilidade na Tomada de Decisão:**
  
    -  Dados tratados e atualizados automaticamente alimentam dashboards e relatórios em tempo real.
      
    -  Menos tempo entre o dado bruto e a informação útil.


## Conclusão

Este projeto evidencia minha capacidade de desenvolver pipelines de dados robustos e escaláveis, aplicando as melhores práticas de engenharia de dados na nuvem com Azure. A solução entregue não apenas automatiza um processo crítico, mas também melhora a governança, a eficiência e a qualidade das informações disponíveis para a tomada de decisão empresarial.


## Contato

- **Autor:** Kleber Goes da Silva
  
- **E-mail:** kleber-goes@hotmail.com
  
- **LinkedIn:** https://www.linkedin.com/in/kleber-goes-02091990/
