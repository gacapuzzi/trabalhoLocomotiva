# Trabalho Locomotiva - Solução de Ingestão e Processamento de Dados para Transporte Público

## 1. Visão Geral do Projeto

### Objetivo
Este projeto visa criar uma solução de ingestão e processamento de dados para monitorar, em near real-time, a frota de ônibus da cidade de São Paulo. A solução permite a coleta de dados quase em tempo real, processamento e visualização de KPIs relevantes.

### Arquitetura
A arquitetura utiliza Apache NiFi para ingestão de dados, MinIO como Data Lake, e Hive e PostgreSQL como camadas de dados para disponibilização de dados transformados (camada Gold). O processamento final visa enriquecer os dados com informações de localização e gerar insights que podem ser consumidos via consultas SQL dentro do warehouse bem como a visualização de KPIs disponíveis para o time de negócios utilizando a ferramenta do PowerBI.

---

## 2. Arquitetura Técnica

### Ferramentas Utilizadas
- **Apache NiFi**: Utilizado para o fluxo de ingestão de dados.
- **MinIO**: Responsável por armazenar os dados brutos e processados no formato de Data Lake.
- **Hive**: Utilizado para a criação de tabelas que armazenam dados transformados.
- **PostgreSQL**: Usado para a camada Gold, onde os dados tratados ficam prontos para análises e consumo.
- **PowerBI**: Ferramenta utiizada para visualização de KPIs e analises/estratégias pertinentes ao negócio.

### Diagrama de Arquitetura
- A ingestão de dados começa pela API OLHO VIVO e os dados complementares do GTFS.
- Apache NiFi processa os dados e armazena no MinIO, que está organizado em três camadas (RAW, Trusted, Gold).
- Hive e PostgreSQL são usados para realizar consultas e transformar os dados, para que possam ser consumidos via consultas SQL e tenha demais aplicações voltadas para o negócio.
- PowerBI tem como sua funcionalidade gerar a camada de datavviz para utilização do negócio.

---

## 3. Ingestão de Dados com Apache NiFi

### Descrição
Apache NiFi foi utilizado para coletar dados da API OLHO VIVO da SPTRANS, que fornece a posição de todos os ônibus em circulação a cada dois minutos. 
A consumo de dados GTFS é utilizado para dados complementares (dados cadastrais e de paradas).

### Configurações Importantes
- **API OLHO VIVO**:
  - Coleta de dados a cada 2 minutos.
  - Autenticação via token fornecido pela API.
- **API GTFS**:
  - Dados estáticos utilizados para enriquecer os dados com informações cadastrais.

**Endpoints Utilizados**:
- API OLHO VIVO: [Documentação](https://www.sptrans.com.br/desenvolvedores/api-do-olho-vivo-guia-de-referencia/documentacao-api/)
- GTFS: [Documentação](https://gtfs.org/documentation/schedule/reference/)

---

## 4. Armazenamento de Dados no MinIO

### Descrição
O MinIO é utilizado como um Data Lake com três camadas de dados:
1. **RAW**: Contém os dados brutos coletados pelas APIs.
2. **Trusted**: Contém os dados validados e organizados após o processamento.
3. **Gold**: Dados tratados, prontos para análise e consumo.

### Estrutura de Buckets
- **/raw**: Dados brutos não processados.
- **/trusted**: Dados após validação e limpeza.
- **/gold**: Dados prontos para análise.

**Configuração do MinIO**:
- Armazenamento no formato de objetos (ex: JSON ou CSV).
- Controle de acesso e autenticação via credenciais definidas no Docker Compose.

---

## 5. Processamento de Dados e Camada Gold

### Hive
- O Hive é utilizado para armazenar e processar os dados da camada Trusted, aplicando regras de negócio e transformações.
- Estrutura de tabelas que permitem consultas SQL para extração dos dados.

### PostgreSQL
- Utilizado como banco de dados para armazenar os dados finais (camada Gold), prontos para serem consumidos por ferramentas de visualização como Metabase.
- Tabelas organizadas para permitir consultas otimizadas e entrega de KPIs.

---

## 6. Implementação com Docker

### Configuração do Ambiente
O projeto utiliza contêineres Docker para gerenciar os serviços envolvidos (NiFi, MinIO, PostgreSQL, Hive).

### Arquivos Importantes:
- **Dockerfile**: Descreve a configuração dos contêineres para cada serviço.
- **docker-compose.yaml**: Orquestra os serviços, definindo a rede, volumes e variáveis de ambiente.

**Comandos para Iniciar o Ambiente**:
```bash
docker-compose up -d
