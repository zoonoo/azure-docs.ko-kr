---
title: Azure 데이터 탐색기 데이터 시각화
description: Azure Data Explorer 데이터를 시각화할 수 있습니다 다른 방법에 알아봅니다
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: d1c73d8eb65ed5d67d5250b4a3bca3b80450001e
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536727"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Azure 데이터 탐색기를 사용 하 여 데이터 시각화 

Azure 데이터 탐색기는 방대한 양의 데이터에 대 한 복잡 한 분석 솔루션을 구축 하는 데 사용 되는 로그 및 원격 분석 데이터에 대 한 빠르고 확장성이 높은 데이터 탐색 서비스입니다. Azure 데이터 탐색기는 데이터를 시각화 하 고 조직 전체에서 결과 공유할 수 있도록 다양 한 시각화 도구와 통합 됩니다. 이 데이터를 비즈니스에 영향을 줄 수 있도록 실행 가능한 정보로 변환할 수 있습니다.

데이터 시각화 및 보고 데이터 분석 프로세스의 중요 한 단계입니다. Azure 데이터 탐색기는 예산 고 시나리오에 가장 잘 맞는 하나를 사용할 수 있도록 다양 한 BI 서비스를 지원 합니다.

## <a name="kusto-query-language-visualizations"></a>Kusto 쿼리 언어 시각화

Kusto 쿼리 언어 [ `render operator` ](/azure/kusto/query/renderoperator) 테이블, 원형 차트 및 쿼리 결과 보여 주는 가로 막대형 차트와 같은 다양 한 시각화를 제공 합니다. 쿼리 시각화는 변칙 검색 및 예측, machine learning 등에서 유용 합니다.

## <a name="power-bi"></a>Power BI

에 연결 하는 기능을 제공 하는 azure 데이터 탐색기 [Power BI](https://powerbi.microsoft.com) 다양 한 메서드를 사용 하 여: 

  * [기본 제공 기본 Power BI 커넥터](/azure/data-explorer/power-bi-connector)

  * [Power BI로 Azure 데이터 탐색기에서 쿼리 가져오기](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL query](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

에 연결 하는 기능을 제공 하는 azure 데이터 탐색기 [Microsoft Excel](https://products.office.com/excel) 커넥터를 Excel 또는 Excel로 Azure 데이터 탐색기에서 쿼리를 가져올 기본 네이티브를 사용 하 여 합니다.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) Azure 데이터 탐색기에서 데이터를 시각화할 수 있도록 하는 Azure 데이터 탐색기 플러그 인을 제공 합니다. 있습니다 [Grafana에 대 한 Azure 데이터 탐색기 데이터 원본으로 설정 하 고 다음 데이터를 시각화](/azure/data-explorer/grafana)합니다. 

## <a name="odbc-connector"></a>ODBC 커넥터

제공 하는 azure 데이터 탐색기는 [개방형 데이터베이스 연결 (ODBC) 커넥터](connect-odbc.md) ODBC를 지 원하는 응용 프로그램 Azure 데이터 탐색기를 연결할 수 있도록 합니다.

## <a name="tableau"></a>Tableau

에 연결 하는 기능을 제공 하는 azure 데이터 탐색기 [Tableau](https://www.tableau.com) 를 사용 하는 [ODBC 커넥터](/azure/data-explorer/connect-odbc) 차례로 [Tableau에서 데이터를 시각화](tableau.md)합니다.

## <a name="qlik"></a>Qlik

에 연결 하는 기능을 제공 하는 azure 데이터 탐색기 [Qlik](https://www.qlik.com) 사용 하는 [ODBC 커넥터](/azure/data-explorer/connect-odbc) 및 Qlik Sense 대시보드를 만들고 데이터를 시각화 합니다. 다음 비디오를 사용 하 여, Qlik 사용 하 여 Azure Data Explorer 데이터 시각화를 배울 수 있습니다. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

에 연결 하는 기능을 제공 하는 azure 데이터 탐색기 [Sisense](https://www.sisense.com) JDBC 커넥터를 사용 합니다. 있습니다 [Sisense에 대 한 Azure 데이터 탐색기 데이터 원본으로 설정 하 고 다음 데이터를 시각화](/azure/data-explorer/sisense)합니다.