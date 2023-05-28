# BD_SQL_Demonstracoes_Fiscais
Este projeto tem como objetivo a criação de tabelas com inserção dos dados a partir da leitura de arquivos CSV, além da realização de consultas SQL. Para o projeto foi utilizado o PostgreSQL versão 15.3.

O programa lê arquivos CSV contendo as informações financeiras das operadoras e as armazena em um banco de dados PostgreSQL. Em seguida, são executadas consultas SQL para obter as 10 operadoras com maiores despesas em determinados períodos.

## Banco de Dados

O projeto utiliza o PostgreSQL 15.3 como banco de dados. Certifique-se de ter o PostgreSQL instalado em seu ambiente antes de executar o programa.

## Estrutura das Tabelas

O projeto utiliza duas tabelas principais: "ralacao_de_operadoras_ativas" e "demonstracoes_contabeis". 
- ralacao_de_operadoras_ativas: armazena informações das operadoras de saúde ativas.
- demonstracoes_contabeis: armazena as demonstrações contábeis das operadoras.

- Tabela ralacao_de_operadoras_ativas:
  - registro_ans: Chave primária (int).
  - cnpj: CNPJ da operadora (varchar).
  - razao_social: Razão social da operadora (varchar).
  - nome_fantasia: Nome fantasia da operadora (varchar).
  - ... (demais campos contendo informações da operadora)

- Tabela demonstracoes_contabeis:
  - id: Chave primária (serial).
  - data_data: Data da demonstração contábil (date).
  - reg_ans: Referência ao registro_ans da tabela ralacao_de_operadoras_ativas (int).
  - cd_conta_contabil: Código da conta contábil (int).
  - descricao: Descrição da conta contábil (varchar).
  - vl_saldo_final: Valor do saldo final da conta contábil (double precision).

Os arquivos CSV estão organizados em trimestres dos anos de 2019 e 2020 para cada tabela. Cada arquivo contém os dados correspondentes a um trimestre específico.

Para importação dos dados dos CSV foi-se necessário o uso de:
   - COPY: Comando utilizado para importar os dados dos arquivos CSV para as tabelas do banco de dados.
   - ENCODING 'ISO-8859-1': Parâmetro utilizado para especificar a codificação correta dos arquivos CSV.

## Uso de Tabela Temporária

Foi necessário utilizar uma tabela temporária chamada "tabela_temporaria" (semelhante a tabela demonstracoes_contabeis) para processar os dados dos arquivos CSV antes de inseri-los na tabela "demonstracoes_contabeis". Isso se deve ao fato de que os valores do campo "vl_saldo_final" nos arquivos CSV estão formatados com vírgula como separador decimal, enquanto o PostgreSQL utiliza o ponto como separador decimal. A tabela temporária lê os valores como strings e, em seguida, são inseridos na tabela "demonstracoes_contabeis" após a conversão para o tipo de dado "double precision".

- Tabela tabela_temporaria:
  - data_data: Data da demonstração contábil (date).
  - reg_ans: Referência ao registro_ans da tabela ralacao_de_operadoras_ativas (int).
  - cd_conta_contabil: Código da conta contábil (int).
  - descricao: Descrição da conta contábil (varchar).
  - vl_saldo_final: Valor do saldo final da conta contábil (varchar).

## Consultas SQL

- Consulta 1: Obtém as 10 operadoras com maiores despesas com "EVENTOS/ SINISTROS CONHECIDOS OU AVISADOS DE ASSISTÊNCIA A SAÚDE MEDICO HOSPITALAR" no último trimestre de 2020.
- Consulta 2: Obtém as 10 operadoras com maiores despesas com "EVENTOS/ SINISTROS CONHECIDOS OU AVISADOS DE ASSISTÊNCIA A SAÚDE MEDICO HOSPITALAR" no ano de 2019.

A fim de calcular as despesas das empresas com base nos valores do arquivo CSV fornecido, utiliza-se a coluna "VL_SALDO_FINAL" para a conta contábil específica relacionada às despesas. A descrição dessa conta contábil é "EVENTOS/ SINISTROS CONHECIDOS OU AVISADOS DE ASSISTÊNCIA A SAÚDE MEDICO HOSPITALAR".

Para obter as despesas das empresas, é realizada uma junção (JOIN) entre as tabelas "ralacao_de_operadoras_ativas" e "demonstracoes_contabeis" com base no campo "registro_ans", que é uma coluna relacionando as duas tabelas. Em seguida, é aplicado um filtro (WHERE) para selecionar apenas os registros que correspondem à conta contábil específica de despesas.

A soma dos valores da coluna "VL_SALDO_FINAL" é calculada usando a função de agregação SUM(). O resultado é agrupado (GROUP BY) pela razão social das operadoras e ordenado (ORDER BY) em ordem decrescente com base no valor total das despesas. Por fim, é limitado o número de resultados (LIMIT) para exibir apenas as 10 principais operadoras de saúde com as maiores despesas.

Essa abordagem permite calcular as despesas das empresas com base nos valores financeiros fornecidos no arquivo CSV e na conta contábil específica relacionada às despesas.

## Como Utilizar

Siga as etapas abaixo para utilizar o projeto:

1. Clone o repositório do projeto para o seu ambiente local.
```shell
git clone https://github.com/MrGabrielBP/BD_SQL_Demonstracoes_Fiscais.git
```
2. Certifique-se de ter o PostgreSQL instalado e configurado.
3. No arquivo do código SQL, defina os caminhos completos (dentro do comando COPY) para os arquivos CSV que você deseja importar para o banco de dados (os arquivos CSV utilizados estão aqui no repositório do GitHub.
4. Execute o código SQL no seu ambiente PostgreSQL para criar as tabelas e importar os dados dos arquivos CSV.
5. Após a importação dos dados, você pode executar as queries mencionadas para obter os relatórios de despesas das operadoras.
