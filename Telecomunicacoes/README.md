#O Desafio

A sua empresa de telecomunicações onde você trabalha anunciou a aquisição da TELCO. TELCO é uma empresa jovem e com muito potencial, cujas filiais estão localizadas em 1.106 cidades do estado da Califórnia, Estados Unidos.

O objetivo é avaliar desafios em termos de perdas de cliente da TELCO. Atualmente a principal hipótese é a concorrência acirradas, principalmente na região de San Diego e a solução proposta é investir em contratos de longo prazo.

O desafio consiste em validar ou não essa hipótese, através da exploração do banco de dados e contrato atuais e passados.
Obter  o Cluster de clientes de “Alto risco de abandono e de alto retorno financeiro".

##Skills/Tools:
  -Power BI
  -Google Cloud
  -SQL
  -Análise de Dados
  -Decisões de Negócios apoiadas por dados
  
 #Resolução

-[Dashboard no Power BI](https://app.powerbi.com/view?r=eyJrIjoiYmFiYTcwMzUtNDFiNS00NDAwLThlYzUtOTg5ZWI4ODNhYzdjIiwidCI6IjYzYTNiY2VhLTk1ZWEtNDVlZC05YWE4LTA1Yjk3ZDkwODM2MCJ9)

##1. Entendendo os dados
As seguintes tabelas foram fornecidas:
-*Services*: Informações dos serviços contratatos pelo cliente, assim como os valores pagos mensalmentes, cobranças extras, e indicações de clientes.
-**Churn_demographics**: Informações sobre o cliente como gênero, idade, estado civil e número de filhos.
-**location**: Informações geograficos sobre o cliente.
-**population**: Informações sobre a população de cada cidade
-**status**: Informações sobre a perda de clientes, status atual do cliente e motivo de cancelamento.

##1. Preparando os dados
### Unificando em uma única tabela


'''
CREATE TABLE  `perda-de-clientes.churn.master_view` as (
SELECT l.* EXCEPT(Zip_Code),
d.Gender,d.Age, d.Under_30, d.Senior_Citizen,d.Married,d.Number_of_Dependents, s.*EXCEPT (Customer_ID, Count),
st.Churn_Label,st.Churn_Category, st.Churn_Value, st.Churn_Reason
FROM perda-de-clientes.churn.location as l
LEFT JOIN perda-de-clientes.churn.churn_demographics as d
ON l.Customer_ID=d.Customer_ID
LEFT JOIN perda-de-clientes.churn.Services as s
ON d.Customer_ID=s.Customer_ID
LEFT JOIN perda-de-clientes.churn.status as st
ON s.Customer_ID=st.Customer_ID)
'''

###Explorando os dados
- Número de Registros: 7043
'''
SELECT COUNT(Customer_ID) FROM `perda-de-clientes.churn.master_view` ;

'''

- Explorar idade dos usuários:
'''
SELECT 
AVG(Age) AS idade_media,  #Resultado: 47,5
MIN(Age) as idade_minima, #Resultado: 19
MAX(Age) as idade_maxima  #Resultado: 119
from `perda-de-clientes.churn.master_view` ;
'''
- Número de Clientes por sexo:
F - 638	
Male - 2918
Female - 2850
M - 637
'''
SELECT DISTINCT Gender,
COUNT(Customer_ID) AS clientes_totais
FROM `perda-de-clientes.churn.master_view`
GROUP BY
1;
'''
- Explorar pagamentos mensais
63.5961309101235
	
-10.0
	
118.75
'''
SELECT 
AVG(Monthly_Charge) AS pagamento_medio,   #Resultado: $63,6
MIN(Monthly_Charge) AS pagamento_minimo,  #Resultado: -$10.00
MAX(Monthly_Charge) AS pagamento_maximo   #Resultado: $118,75
FROM `perda-de-clientes.churn.master_view`;
'''
- Explorar Receita por trimestre por cliente
'''
SELECT 
AVG(Total_Revenue) AS receita_media,    #Resultado: $3034,40
MIN(Total_Revenue) AS receita_minima,   #Resultado: $21,36
MAX(Total_Revenue) AS receita_maxima    #Resultado: 11979,34
FROM `perda-de-clientes.churn.master_view`;
'''
- Explorar estado civil		
Solteiros: 3641
Casados: 3402
'''
SELECT DISTINCT Married,
COUNT(Customer_ID) AS clientes_totais
FROM `perda-de-clientes.churn.master_view`
GROUP BY
1;
'''
- Explorar cidades com maior número de clientes
Los Angeles: 293
San Diego: 285
San Jose: 112	
Sacramento: 108
San Francisco: 104
'''
SELECT DISTINCT City,
COUNT(Customer_ID) AS clientes_totais
FROM `perda-de-clientes.churn.master_view`
GROUP BY
City
ORDER BY clientes_totais DESC
LIMIT 5
'''
### Limpeza da tabela
Apagar registros com idade maior que 95, com monthly charges menor que 0 e unificar sexo em Male e Female
'''
DELETE  FROM `perda-de-clientes.churn.master_view_limpa` WHERE Age>95;
DELETE  FROM `perda-de-clientes.churn.master_view_limpa` WHERE Monthly_Charge<0;

UPDATE `perda-de-clientes.churn.master_view_limpa`
SET Gender = 'Male'
WHERE Gender = "M";

UPDATE `perda-de-clientes.churn.master_view_limpa`
SET Gender = 'Female'
WHERE Gender = "F";
'''
##2. Segmentação de Clientes:
Segmentação dos clientes por idade, por número de indicações e por tempo de serviço.
- Idade - 

[WIP]

