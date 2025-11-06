# BMG
## DESAFIO TÉCNICO - ENGENHARIA DE DADOS
<br>

## Contexto
Os códigos foram criados dentro do Databricks Community Edition e, dessa forma, estão sujeitos às limitações da plataforma.

A ideia foi emular uma arquitetura Medallion e, dentro de cada etapa, explicar o respectivo comparativo com uma estrutura real de pipeline - isto é, uma estrutura que contenha tecnologias de armazenamento, orquestração e delivery aplicáveis à realidade de uma firma.
<br><br>

## Primeira Etapa - Ingestão Bruta

Aqui, o arquivo foi coletado com um script Shell num Unity Catalog Volume denominado `ans` e dividido nas subpastas `compressed` e `extracted`.

Numa estrutura real, a ingestão seria feita num container de data lake destinado a esses arquivos, e o mecanismo de autoloading do Databricks poderia ser usado para atualizar a camada `raw` com os arquivos .csv recém chegados.
<br><br>

## Segunda Etapa - Padronização

Nessa etapa, seria aplicada uma tipagem condizente com o dicionário disponibilizado pela ANS e uma regra de mascaramento para CNPJ.

Por sua vez, a tabela delta da camada `refined` estaria ligada a um segundo container do delta lake numa estrutura real.
<br><br>

## Terceira Etapa - Destino Analítico

Finalmente, os dados da camada `curated` foram particionados pelos campos relacionados ao padrão de disponibilização dos arquivos: **mês/ano** e **estado**. Trata-se de uma extrapolação mais condizente a conjuntos de dados de maior magnitude, sob risco de recair no problema de _small files_.

Aqui, como exemplo, uma tabela adicional agregada pelos cinco campos discriminados no PDF foi criada para responder às três perguntas.

Além de conectadas a uma um container final do delta lake, tabelas de consumo específico seriam ligadas a conectores de warehousing para disponibilização dos resultados mais visados em termos de negócio.
<br><br>

## Síntese da Estrutura

- Criação de três contâneires num delta lake, responsáveis por abrigar os dados de cada camada da arquitetura. Eles estariam associados ao lakehouse do Databricks, bem como aos seus mecanismos de governança;
- Elaboração de um trigger periódico para captura de novos arquivos disponibilizados pela ANS (via Shell/Python script) e consequente atualização das três camadas (via notebooks Databricks);
- Conexão de tabelas agregadas de consumo final a ferramentas de warehousing para visibilidade por parte dos stakeholders de interesse.

Mais detalhes podem ser encontrados dentro de cada um dos notebooks.
