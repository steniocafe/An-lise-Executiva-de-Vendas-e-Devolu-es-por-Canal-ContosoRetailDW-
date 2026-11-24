# 📊 Análise Executiva de Vendas e Devoluções por Canal (ContosoRetailDW)

Projeto de Business Intelligence desenvolvido para responder a uma demanda executiva: identificar quais canais geram maior faturamento para a empresa e onde estão concentradas as perdas financeiras com devoluções.

---

## 🎯 Contexto de Negócio
A liderança solicitou um painel gerencial para responder a duas perguntas-chave sobre os canais **Store**, **Online**, **Reseller** e **Catalog**:
1. **Por qual canal a empresa vende mais?**
2. **Onde estamos perdendo dinheiro com devoluções (em valor absoluto e taxa percentual)?**

---

## 🛠️ Arquitetura e Tecnologias Utilizadas
* **Banco de Dados & SQL:** SQL Server (`ContosoRetailDW`) para exploração inicial, validação de regras de negócio, tratamento contra divisão por zero (`NULLIF`) e formatação executiva.
* **Modelagem de Dados:** Star Schema / Snowflake integrando `FactSales`, `DimChannel`, `DimDate`, `DimProduct`, `DimProductSubcategory` e `DimProductCategory`.
* **Visualização & DAX:** Power BI Desktop com medidas dinâmicas de faturamento, devoluções, taxa percentual (`% Devolução`) e participação (`Share Vendas`).
* **Design UI/UX:** Tela de fundo (*wallpaper*) em modo escuro desenvolvida sob medida com o auxílio do **Google Gemini**, estruturando a hierarquia visual para os cartões de KPI, gráficos comparativos e matriz hierárquica.

---

## 📈 Principais Insights Encontrados
* **Liderança de Receita:** O canal **Store** representa **55,93%** do faturamento total (**R$ 6,94 Bi**), seguido pelo **Online** com **21,57%** (**R$ 2,68 Bi**).
* **Ponto de Atenção em Devoluções:** Além de liderar o volume financeiro devolvido (**R$ 90,64 Mi**), o canal **Store** também apresenta a maior taxa proporcional de devolução (**1,31%**), superando a média global da operação (**1,26%**).
* **Eficiência Operacional:** O canal **Catalog** possui o menor índice de devolução (**1,10%**), mantendo a perda financeira controlada em **R$ 11,91 Mi**.

---

<img width="477" height="120" alt="image" src="https://github.com/user-attachments/assets/0e4f791d-d0b1-4d4a-aa66-497486b93451" />

## 💻 Query SQL de Validação
```
USE ContosoRetailDW;

SELECT
    C.ChannelName,
    FORMAT(SUM(S.SalesAmount), 'C', 'pt-BR')                                                      AS Total_Vendas, 
    FORMAT(SUM(S.ReturnAmount), 'C', 'pt-BR')                                                     AS Total_Devoluções,
    CONCAT(FORMAT(SUM(S.ReturnAmount) / NULLIF(SUM(S.SalesAmount), 0) * 100, 'N2', 'pt-BR'), '%') AS [%_Devoluções]
FROM FactSales AS S
JOIN DimChannel AS C ON C.ChannelKey = S.ChannelKey
GROUP BY C.ChannelName
ORDER BY SUM(S.SalesAmount) DESC;
´´´
