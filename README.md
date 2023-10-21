# DIO | Desafio de Projeto - Processando e Transformando Dados com Power BI

Este repositório tem como objetivo resolver o desafio de projeto da DIO sobre Power BI, parte do Santander Bootcamp 2023 - Ciência de Dados com Python.

O desafio propõe que os dados sejam extraidos por meio de uma conexão com um banco de dados MySQL na Microsoft Azure. Porém, como para o cadastro é exigido um cartão de crédito (mesmo que não seja feita cobranças futuras), optei por não utilizar a Microsoft Azure.

Assim, o banco de dados utlizado foi criado localmente, através do  MySQL configurado juntamente com o DBeaver, seguindo tutoriais disponíveis no Youtube no [Canal dataworldbr](https://www.youtube.com/watch?v=-4DGTKXz3A4&t=245).

Agradecimento especial para [Ana Guernelli](https://www.linkedin.com/in/ana-beatriz-guernelli-9937a8209/) que deu dicas valiosas no fórum da DIO, e de quem extrai o script para inserção de dados.

 O Power BI então extrai os dados de um banco local, ao invés de um banco criado em nuvem.

# Roteiro

## 1. Carregamento e transformação de dados

Após a adequação da base de dados usando as instruções do arquivo [Insercao_de_dados.txt], foi feito o link com o Power BI através da função Obter dados / Banco de dados MySQL.

Em seguida, os dados não utilizados foram excluídos das tabelas, deixando-as mais adequadas para a utilização e geração do relatório.

Os dados foram transformados no Power Query, conforme as instruções contidas em [Desafio de Projeto - Instruções.docx], seguindo a seguinte ordem:

Na tabela **employee**
- mudança no tipo de dado da coluna *Ssn* de texto para número inteiro;
- separação da coluna *Address* em 4: **AddressNumber**, **Address**, **City** e **State**;
- mudança no tipo de dado da coluna *Salary* de número decimal para número decimal fixo;
- substituição do valor *null* na coluna *Super_ssn* pelo valor **888665555**, já que esse colaborador é o o único no departamento *Headquartes* e supervisor dos demais, ele seria seu próprio supervisor.
- mudança no tipo de dado da coluna *Super_ssn* de texto para número inteiro;

Na tabela **departament**
- mudança no tipo de dado da coluna *Mgr_ssn* de texto para número inteiro;

Na tabela **dependent**
- mudança no tipo de dado da coluna *Essn* de texto para número inteiro;

Na tabela **works_on**
- mudança no tipo de dado da coluna *Essn* de texto para número inteiro;

## 2. Criação de uma nova tabela

Usando o método Combinar / Mesclar Consultas / Mesclar Consultas como Novas, a partir da tabela *employee*, foi feita a mescla desta com a tabela *departament*, gerando uma nova tabela [employee_departament].

Nesta nova tabela gerada, a coluna **company_az departament** foi expandida para mostrar seu conteúdo.
As colunas *company_az departament.Dnumber* e *company_az departament.Mgr_ssn* foram excluídas pois essas informações já existiam na tabela *employee*.

Uma nova mesclagem foi feita, com as tabelas *departament* e *employee*, com o objetivo de extrair os nomes dos gerentes de cada departamento. A nova tabela gerada foi nomeada de [departament_manager]. Desta tabela foram excluidas as colunas *Mgr_start_date*, *Dept_create_date*, *company_az.empoyee.Bdate*, *company_az.empoyee.AddressNumber*, *company_az.empoyee.Address*, *company_az.empoyee.City*, *company_az.empoyee.State*, *company_az.empoyee.Ssn*, *company_az.empoyee.Super_ssn* e *company_az.empoyee.Dno*.

Foi criada uma nova coluna na tabela *employee*, mesclando as colunas *Fname*, *Mint* e *Lname*, gerando a coluna *Name*. Após as referidas colunas foram excluídas.

As tabelas *dept_location* e *departament* também foram mescladas, usando a primeira como base. Dessa forma, os dados da tabela *departament* foram inseridos na tabela *dept_location*, sem gerar uma nova tabela. As colunas *company_az.departament.Dnumber*, *company_az.departament.Mgr_start_date* e *company_az.departament.Dept_create_date* forma excluídas.

O número de colaboradores por gerente pode ser consultado no relatório do Power BI. Porém também pode ser feita uma pequisa pelo MySQL diretamente no database (o script pode ser usado para gerar outra tabela no Power BI).

```
select Dname as Departament, count(*) as Total_Colaborador from employee e
	inner join departament d on e.Dno = d.Dnumber group by Dname;
```
| Departamento | Colaboradores |
|-----|------|
| Research | 4 |
| Headquarters | 1 |
| Administration | 3 |

## 3. Diferença entre mesclar e acrescentar:

Quando usamos o **acrescentar**, o Power Query junta as tabelas em uma só, concatenando as suas linhas. Útil quando as colunas tem os mesmos dados, porém por algum motivo foram armazenados em tabelas diferentes. Mas as tabelas devem ter as mesmas colunas. Caso contrário, as colunas que forem diferentes entre as tabelas serão consideradas dados faltantes e preenchidas como *null*, podendo causar problemas ass análises a serem feitas.

Já com o mesclar, você deve designar qual a coluna que será usada como base para a mescla dos dados. Assim, o Power Query irá procurar os dados correspondentes da primeira tabela na segunda, acrescentando colunas com os dados da segunda tabela.
