# Data Warehouse e Análise de Dados ENADE


![GitHub repo size](https://img.shields.io/github/repo-size/leticiatavaresds/Projeto_ENADE?color=a21360&style=for-the-badge)
![GitHub language count](https://img.shields.io/github/languages/count/leticiatavaresds/Projeto_ENADE?color=a21360&style=for-the-badge)
![Made With](https://img.shields.io/badge/Made%20With-Python-lightgrey?color=a21360&style=for-the-badge)
![GitHub repo file count](https://img.shields.io/github/directory-file-count/leticiatavaresds/Projeto_ENADE?color=a21360&style=for-the-badge)
![GitHub last commit](https://img.shields.io/github/last-commit/leticiatavaresds/Projeto_ENADE?color=a21360&style=for-the-badge)


## Índice

- [Introdução](#introdução)
- [Microdados ENADE](#microdados-enade)
- [Obtenção dos Dados](#obtenção-dos-dados)
- [Modelagem de Dados](#modelagem-de-dados)
- [Tratamento de Dados](#tratamento-de-dados)
- [Análise Exploratória](#análise-exploratória)
- [Modelo de Predição](#modelo-de-predição)
- [Execução](#execução)
- [Licença](#licença)

# Introdução

O escopo do projeto é dividido em três partes. Primeiramente, é construído um Data Warehouse seguido de uma modelagem dimensional estrela com os microdados do ENADE do anos de 2017, 2018 e 2019 disponibilizados no site do INEP. A partir de consultas ao Data Warehouse elaborado, é então realizada uma Análise Exploratória para por fim construir um modelo de aprendizagem de máquina com a finalidade de predizer o desempenho de um aluno com base em informações preenchidas nas provas como dados da instituição, características e perfil socioeconômico do aluno. As bases de dados importadas possuem 169 variáveis, mas no presente projeto algumas foram descartadas. 

[⬆ Voltar ao topo](#data-warehouse-e-análise-de-dados-enade)<br>

# Microdados ENADE

O ENADE, sigla para Exame Nacional de Desempenho de Estudantes, é uma exame anual que tem como objetivo avaliar o conhecimento obtidos em relação aos conteúdos previstos na grade curricular do respectivo curso de graduação dos estudantes acadêmicos que estão prestes a se formar. Dessa forma, todo ano, alunos concluintes do ensino superior são submetidos a uma prova de conhecimento geral e específico composta por questões objetivas e discursivas. Além das questões para avaliar o conhecimento do estudante, as provas também apresentam questionários para obter informações socionômicas e experiências acadêmicas do estudante, além de seu preparo para a prova, percepção sobre a mesma e avaliação da Instituição. Todas essas informações obtidas em cada ano são disponibilizadas pelo INEP como microdadados.

[⬆ Voltar ao topo](#data-warehouse-e-análise-de-dados-enade)<br>

# Obtenção dos Dados
Para o desenvolvimento deste trabalho foram utilizados os microdados do Enade disponibilizados pelo site do INEP. A pesquisa se baseou em dados de todos os estudantes que realizaram o exame no ano de 2017, 2018 e 2019, totalizando 182 atributos na base de 2017 e 169 em cada umas das outras bases. Essa diferença se dá por varáveis que estão presentes apenas na base de 2017 referentes à questões específicas do curso de licenciatura. Dessa forma, como esses atributos são específicos para um grupo de cursos e não estão presentes em todas as bases, dificultando assim uma integração para uma análise exploratória, foram descartados neste trabalho.

As informações são disponibilizadas no formato TXT acompanhadas de um dicionário de variáveis no formato XLS que apresenta uma aba com a descrição do que significa cada atributo e seus possíveis valores e outra aba com atributos identificadores de cada código de cidade disponibilizados pelo IBGE, já que os microdados aprensentam apenas esses códigos na identifição do local do curso.

Além dos dados descritos acima, foram também utilizados nesse trabalho as bases também fornecidas pelo INEP que apresentam o Conceito ENADE de cada ano e podem ser encontradas na página do INEP. Essas bases foram utilizadas com a finalidade de obter atributos referentes à alguns códigos que aparecem no microdados do ENADE como nome da instituição.

Para realizar extração de todas os dados listados anteriormente de forma automatizada, utilizou-se o script ```“00_Download_Dados”``` desenvolvido em um notebook jupyter com linguagem python que ao ser executado, realiza o donwload dos microdados do enade e dos dados do Conceito enade e os salva em uma pasta denominada de “Dados” no mesmo diretório em que se encontra o script.

[⬆ Voltar ao topo](#data-warehouse-e-análise-de-dados-enade)<br>

# Modelagem de Dados

No presente trabalho foi utilizada a modelagem Estrela (Star Schema) caracterizada por poucas tabelas e relacionamentos, onde todas as tabelas de Dimensão se relacionam direta e unicamente com a tabela Fato, sendo assim um modelo simples e eficiente.

O modelo elaborado neste trabalho foi desenvolvido com o intuito de ser centrado nas notas das provas, objetivando fornecer dados para análise de fatores que influenciam no desempenho de cada aluno que presta a avaliação. Dessa forma, o modelo apresenta como fato a prova que possui como métrica a nota final e as notas de formação geral e componente específico que a compoem, além de 11 tabelas de dimensão. A modelagem pode ser observada no diagrama abaixo que foi elaborado com a ferramenta Visual Paradigm:

<img align="center" width="900" src="https://github.com/leticiatavaresds/Projeto_ENADE/blob/main/Modelo%20Dimensional%20Estrela/ENADE.png" />

[⬆ Voltar ao topo](#data-warehouse-e-análise-de-dados-enade)<br>

# Tratamento de Dados
Para construção do modelo, o dataset passou foi avaliado e passou por tratamento de dados. O dataset apresenta algumas linhas que não apresentam notas das provas, escolhidas para serem fatos da modelagem e target do modelo, assim foi decidido pela remoção desses casos. Também foi optado pela remoção de nulos da coluna “QI_01” já que foi oberservado que essas amostras também apresentavam valores nulos em quase todas as outras colunas pertencetes ao questionário. Uma outra remoçào foi a de amostras com idades duvidosas como 4 e 11 anos, assim foi criado um filtro para só incluir linhas que apresentam idade maior ou igual à 16. Assim, após todas as remões feitas, o dataset final ficou com 1.291.772 linhas.

Outro tratamento feito foi a substituição de valores pelos seus significados apresentados no dicionário de dados nas colunas. Algumas colunas possuiam valores diferentes em cada ano que possuiam o mesmo significado, nesses casos, foram obtidas informaçòes de todos os dicionários para que todos os valores fossem substuídos, ocasionando numa mesma substituição para valores diferentes.

Todas essas manipulações podem ser observadas no script python “01_Criação_Banco_de_Dados.ipynb” na sessão onde as tabelas de dimensão e fato são construídas.

Foi realizada uma Análise de Dados a partir do Data Warehouse construído. Nessa etapa, alguns dos dados foram utilizados para a realização de análises com o objetivo de avaliar a possível influência de alguns fatores socioeconômicos e geográficos na nota de um aluno.

[⬆ Voltar ao topo](#data-warehouse-e-análise-de-dados-enade)<br>

# Análise Exploratória

A análise foi realizada no notebook “02_Analise_Exploratória_Dados.ipynb” utilizando python versão 3.8.8 como linguagem de programação onde foram utlizadas as bibliotecas sqlite3 (para conexão com o banco de dados), researchpy (para gerar tabela de análise), plotly (para visualização). O notebook se encontra com as linhas que geram plots comentadas, pois o arquivo fica muito grande e exige muito de ram se forem descomentadas.
# DW_ENADE
Repositório para armazenar o trabalho final para a matéria de Data Warehouse do período de 2021.1

# Modelo de Predição
Para esse trabalho foi elaborado um modelo de predição que com base alguns dos dados fornecidos pelo estudante (todos os selecionados para compor as dimensões do modelo estrela desenvolvido) classifique se o estudante tirou uma nota acima da nota média do ano em que prestou a prova. Primeiramente, calculou-se a nota média de cada ano utlizando toda a base, com o conhecimento desses valores, criou-se a coluna target que indica se a nota geral do estudante é maior do que a nota média do ano, assim, se o estudante obteve uma nota maior do que a nota geral média do ano em que realizou a prova, a variável será igual à 1, caso contrário, será 0. O modelo tem então o objetivo de classificar essa variável como 1 ou 0 para cada estudante, classificando assim seu desempenho.

O modelo foi implementado na linguagem python e pode ser visto no scrpit “03_Aprendizado_Random_Forest_Nota.ipynb”. O algoritmo escolhido para compor o modelo foi o Random Forest com parâmetros iguais a máxima profundadade = 12, número de árvores = 5. O moledo foi treinado com 90% dos dados separados de forma aleatória e testado com os outros 10%. As estísticas e matriz de confusão da predição podem ser vistas na imagens abaixo.

Como grande parde das variáveis eram categóricas e todas as variáveis de entrada para o modelo necessitam ser numéricas, foi feita uma conversão dos dados onde variáveis que só assumiam valores “Sim” ou “Não” foram transformadas em booleanda, variáveis que apresentavam alguma informação numérica ou níveis de intensidade de forma categórica foram interpretadas numéricamente como a coluna de Renda Familiar e por último, todas as as que sobraram foram convertidas utilizando o método Mean Encoding.

[⬆ Voltar ao topo](#data-warehouse-e-análise-de-dados-enade)<br>

# Execução

Os Scripts devem ser executados na seguinte ordem:
- 00_Download_Dados.ipynb
- 01_Criação_Banco_de_Dados.ipynb
- 02_Analise_Exploratória_Dados.ipynb
- 03_Aprendizado_Random_Forest_Nota.ipynb

[⬆ Voltar ao topo](#data-warehouse-e-análise-de-dados-enade)<br>

# Licença

The MIT License (MIT) 2022 - Letícia Tavares. Leia o arquivo [LICENSE.md](https://github.com/leticiatavaresds/Projeto_ENADE/blob/master/LICENSE.md) para mais detalhes.

[⬆ Voltar ao topo](#data-warehouse-e-análise-de-dados-enade)<br>
