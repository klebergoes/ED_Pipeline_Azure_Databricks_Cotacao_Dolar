# Projeto de Engenharia de Dados - Dólar comercial (Venda e Compra) - Cotações Diárias

## Sumário

[Desafio / Problema](#Desafio-/-Problema)

[Tecnologias Utilizadas](#Tecnologias-Utilizadas)

[Estrutura do Projeto](#Estrutura-do-Projeto)

[Conjunto de Dados](#Conjunto-de-Dados)

[Metodologia](#Metodologia)


## Desafio / Problema

Uma renomada empresa com sede nos Estados Unidos possui filiais no Brasil, México e Alemanha. Cada unidade realiza compras e vendas em sua respectiva moeda local (BRL, MXN, EUR). Para consolidar as informações financeiras, a matriz precisa converter todas as transações para dólares americanos (USD), garantindo uma visão unificada de receita, custo e lucro por país.

Diariamente, a área de negócios depende desses dados para gerar relatórios financeiros consolidados e, atualmente, esse processo é realizado de forma manual. Para automatizar essa operação, será desenvolvido um pipeline de dados que consome cotações cambiais por meio da API do Banco Central, trata e padroniza as informações, entregando-as em um formato pronto para consumo.

Para atender a esse objetivo, será adotada uma arquitetura de dados moderna baseada em camadas (Bronze, Silver e Gold), utilizando serviços em nuvem da Microsoft Azure. A solução garantirá escalabilidade, governança e eficiência em todo o fluxo de dados.


## Tecnologias Utilizadas

-   Linguagem: Python.
-   API: PySpark.
-   Ferramentas: Azure Data Factory, Azure Datalake Storage, Databricks.


## Estrutura do Projeto

-   Arquitetura_Medalhao: Notebooks (.ipynb) com o desenvolvimento das camadas Silver e Gold da arquitetura em camadas (Medalhão).
-   Factory-Databricks: Arquivos de configuração (JSON) e templates usados em processos de automação e implantação de recursos no Azure, especialmente para Azure Data Factory e Azure Databricks.
-   Dataset: Arquivos de configuração (JSON) que definem a estrutura dos dados e o local de origem e destino para operações de leitura e gravação.
-   Factory: Arquivo de configuração (JSON) relacionados a autenticação e identidade de recursos.
-   LinkedService: Arquivos de configuração (JSON) para conectar o Data Factory a fontes de dados externas ou destinos, permitindo realizar operações de movimentação de dados.
-   Pipeline: Arquivo de configuração (JSON) que descrevem o pipeline no Azure Data Factory.
-   Trigger: Arquivo de configuração (JSON) que descrevem o trigger (gatilho) para o pipeline no Azure Data Factory.
-   .gitignore: Arquivo que contém o nome do notebook (.ipynb) de configuração do ponto de montagem (para o Databricks acessar o Azure Data Lake Storage Gen2), a ser ignorado durante o processo de versionamento. Por ser confidencial, não é incluído no repositório.
-   README.md: Documentação do projeto.
-   Publish_config.json: Arquivo de configuração (JSON) do Databricks com o sistema de versionamento de código.


## Conjunto de Dados

-   Fonte: API PTAX (Ferramenta oficial do Banco Central do Brasil).
-   Endpoint: "https://olinda.bcb.gov.br/olinda/servico/PTAX/versao/v1/odata/CotacaoDolarPeriodo(dataInicial=@dataInicial,dataFinalCotacao=@dataFinalCotacao)?%40dataInicial='05-07-2025'&%40dataFinalCotacao='05-07-2025'&%24format=text%2Fcsv".
-   Retorno: Cotação de Compra e a Cotação de Venda da moeda Dólar contra a unidade monetária corrente para o período informado.
-   Formato: CSV via API.
-   Colunas:
    -   cotacaoCompra: Cotação de compra do dólar contra a unidade monetária corrente: unidade monetária corrente/dólar americano.
        -   Tipo de Dado: decimal.
    -   cotacaoVenda: Cotação de venda do dólar contra a unidade monetária corrente: unidade monetária corrente/dólar americano.
        -   Tipo de Dado: decimal.
    -   dataHoraCotacao: Data, hora e minuto das cotações de compra e venda.
        -   Tipo de Dado: texto.


## Metodologia

1. Ingestão de dados:
