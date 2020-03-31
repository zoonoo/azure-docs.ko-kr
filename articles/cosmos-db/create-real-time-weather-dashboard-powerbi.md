---
title: Azure Cosmos DB, Azure 분석 서비스 및 Power BI를 사용하여 실시간 대시보드 만들기
description: Azure Cosmos DB 및 Azure 분석 서비스를 사용하여 Power BI에서 실시간 날씨 대시보드를 만드는 방법에 대해 알아봅니다.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70376594"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Azure Cosmos DB 및 Power BI를 사용하여 실시간 대시보드 만들기

이 문서에서는 Azure Cosmos DB 및 Azure 분석 서비스를 사용하여 Power BI에서 실시간 날씨 대시보드를 만드는 데 필요한 단계를 설명합니다. Power BI 대시보드에는 지역의 온도 및 강우량에 대한 실시간 정보를 표시하는 차트가 표시됩니다.

## <a name="reporting-scenarios"></a>보고 시나리오

Azure Cosmos DB에 저장된 데이터에 대한 보고 대시보드를 설정하는 방법에는 여러 가지가 있습니다. 부실 요구 사항 및 데이터 크기에 따라 다음 표에서는 각 시나리오에 대한 보고 설정에 대해 설명합니다.


|시나리오 |설치 프로그램 |
|---------|---------|
|1. 임시 보고서 생성(새로 고침 없음)    |  [가져오기 모드가 있는 전원 BI Azure 코스모스 DB 커넥터](powerbi-visualize.md)       |
|2. 주기적인 새로 고침을 통해 임시 보고서 생성   |  [가져오기 모드가 있는 Power BI Azure Cosmos DB 커넥터(예약된 주기적 새로 고침)](powerbi-visualize.md)       |
|3. 대용량 데이터 세트보고(< 10GB)     |  증분 새로 고침을 가진 Power BI Azure Cosmos DB 커넥터       |
|4. 대용량 데이터 세트에 대한 실시간 보고    |  직접 쿼리를 통해 POWER BI Azure 분석 서비스 커넥터 + Azure 분석 서비스(Azure Cosmos DB 커넥터)       |
|5. 집계된 실시간 데이터에 대한 보고     |  [직접 쿼리 + Azure Databricks + Cosmos DB 스파크 커넥터가 있는 Power BI 스파크 커넥터입니다.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. 대규모 데이터 세트에 집계된 라이브 데이터 보고   |  직접 쿼리를 사용하여 POWER BI Azure 분석 서비스 커넥터 + Azure 분석 서비스 + Azure 데이터브릭 + 코스모스 DB 스파크 커넥터.       |

시나리오 1과 2는 Azure Cosmos DB Power BI 커넥터를 사용하여 쉽게 설정할 수 있습니다. 이 문서에서는 시나리오 3 및 4에 대한 설정에 대해 설명합니다.

### <a name="power-bi-with-incremental-refresh"></a>증분 새로 고침을 통해 POWER BI

Power BI에는 증분 새로 고침을 구성할 수 있는 모드가 있습니다. 이 모드를 사용하면 Azure 분석 서비스 파티션을 만들고 관리할 필요가 없습니다. 증분 새로 고침은 큰 데이터 집합의 최신 업데이트만 필터링하도록 설정할 수 있습니다. 그러나 이 모드는 데이터 집합 제한이 10GB인 Power BI Premium 서비스에서만 작동합니다.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>전원 BI Azure 분석 커넥터 + Azure 분석 서비스

Azure Analysis Services는 클라우드에서 엔터프라이즈급 데이터 모델을 호스팅하는 서비스로서 완전히 관리되는 플랫폼을 제공합니다. 대규모 데이터 집합은 Azure Cosmos DB에서 Azure 분석 서비스로 로드할 수 있습니다. 항상 전체 데이터 집합을 쿼리하지 않으려면 데이터 집합을 Azure Analysis Services 파티션으로 세분화할 수 있으며, 이 파티션은 서로 다른 주파수에서 독립적으로 새로 고칠 수 있습니다.

## <a name="power-bi-incremental-refresh"></a>Power BI 증분 새로 고침

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Azure 코스모스 DB에 날씨 데이터 수집

Azure Cosmos DB에 [날씨 데이터를](https://catalog.data.gov/dataset/local-weather-archive) 로드하도록 수집 파이프라인을 설정합니다. Azure [데이터 팩터리(ADF)](../data-factory/connector-azure-cosmos-db.md) 작업을 설정하여 HTTP 소스 및 코스모스 DB 싱크를 사용하여 Azure Cosmos DB에 최신 날씨 데이터를 주기적으로 로드할 수 있습니다.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Azure 코스모스 DB에 전력 BI 연결

1. **Azure Cosmos 계정을 Power BI에 연결** - Power BI 데스크톱을 열고 Azure Cosmos DB 커넥터를 사용하여 올바른 데이터베이스 및 컨테이너를 선택합니다.

   ![Azure Cosmos DB Power BI 커넥터](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **증분 새로 고침 구성** - Power BI 아티클을 [사용하여 증분 새로 고침](/power-bi/service-premium-incremental-refresh) 단계를 수행하여 데이터 집합에 대한 증분 새로 고침을 구성합니다. 다음 스크린샷과 같이 **RangeStart** 및 **RangeEnd** 매개 변수를 추가합니다.

   ![범위 매개 변수 구성](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   데이터 집합에는 텍스트 형식의 Date 열이 있기 때문에 다음 필터를 사용하려면 **RangeStart** 및 **RangeEnd** 매개 변수를 변환해야 합니다. 고급 **편집기** 창에서 쿼리를 수정하여 다음 텍스트를 추가하여 RangeStart 및 RangeEnd 매개 변수를 기반으로 행을 필터링합니다.

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   원본 데이터 집합에 있는 열 및 데이터 형식에 따라 RangeStart 및 RangeEnd 필드를 그에 따라 변경할 수 있습니다.

   
   |속성  |데이터 형식  |Assert  |
   |---------|---------|---------|
   |_ts     |   숫자      |  [_ts] > Duration.TotalSeconds(RangeStart - #datetime (1, 1, 0, 0)) 및 [_ts] < Duration.TotalSeconds(RangeEnd - #datetime (1, 1, 0, 0))) ))       |
   |날짜(예: 2019-08-19)     |   String      | [문서.날짜]> DateTime.ToText(RangeStart,yyyy-MM-dd") 및 [Document.date] < DateTime.ToText(RangeEnd,yyyy-MM-dd")        |
   |날짜(예: 2019-08-11 12:00:00)   |  String       |  [문서.날짜]> DateTime.ToText(RangeStart, yyy-mm-dd HH:mm:ss") 및 [Document.date] < DateTime.ToText(RangeEnd,yyyy-mm-dd HH:mm:ss")       |


1. **새로 고침 정책 정의** - 테이블의 **컨텍스트** 메뉴에서 **증분 새로 고침** 탭으로 이동하여 새로 고침 정책을 정의합니다. 새로 고침 정책을 설정하여 **매일** 새로 고치고 지난 달 데이터를 저장합니다.

   ![새로 고침 정책 정의](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   *M 쿼리를 접을 수 없음을*확인하는 경고를 무시합니다. Azure Cosmos DB 커넥터는 필터 쿼리를 접습니다.

1. **데이터를 로드하고 보고서를 생성** - 이전에 로드한 데이터를 사용하여 온도 및 강우량을 보고할 차트를 만듭니다.

   ![데이터 로드 및 보고서 생성](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **Power BI premium에 보고서 게시** - 증분 새로 고침은 프리미엄 전용 기능이므로 게시 대화 상자는 프리미엄 용량에서 작업 영역만 선택할 수 있도록 합니다. 첫 번째 새로 고침에서 기록 데이터를 가져오는 데 시간이 오래 걸릴 수 있습니다. 이후의 데이터 새로 고침은 증분 새로 고침을 사용하기 때문에 훨씬 빠를 수 있습니다.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>전원 BI Azure 분석 커넥터 + Azure 분석 서비스 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Azure 코스모스 DB에 날씨 데이터 수집 

Azure Cosmos DB에 [날씨 데이터를](https://catalog.data.gov/dataset/local-weather-archive) 로드하도록 수집 파이프라인을 설정합니다. Azure 데이터 팩터리(ADF) 작업을 설정하여 HTTP 소스 및 코스모스 DB 싱크를 사용하여 Azure Cosmos DB에 최신 날씨 데이터를 주기적으로 로드할 수 있습니다.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Azure 분석 서비스를 Azure 코스모스 계정에 연결

1. **새 Azure 분석 서비스 클러스터** - 만들기 Azure Cosmos 계정 및 Databricks 클러스터와 동일한 리전에서[Azure Analysis 서비스의 인스턴스를 만듭니다.](../analysis-services/analysis-services-create-server.md)

1. **시각적 스튜디오에서** -  새 분석 서비스 테이블 형식 프로젝트를[생성 SQL 서버 데이터 도구(SSDT)를 설치하고](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) Visual Studio에서 분석 서비스 테이블 형식 프로젝트를 만듭니다.

   ![Azure 분석 서비스 프로젝트 만들기](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   통합 **작업 영역** 인스턴스를 선택하고 SQL **Server 2017 / Azure 분석 서비스(1400)에** 대한 호환성 수준 설정

   ![Azure 분석 서비스 테이블 형식 모델 디자이너](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **Azure Cosmos DB 데이터 원본 추가** - **모델**> **데이터 원본으로** > 이동**새 데이터 원본으로** 이동하고 다음 스크린샷과 같이 Azure Cosmos DB 데이터 원본을 추가합니다.

   ![코스모스 DB 데이터 소스 추가](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   **계정 URI,** **데이터베이스 이름**및 컨테이너 **이름을**제공하여 Azure Cosmos DB에 연결합니다. 이제 Azure Cosmos 컨테이너의 데이터가 Power BI로 가져온 것을 볼 수 있습니다.

   ![Azure 코스모스 DB 데이터 미리 보기](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **분석 서비스 모델 구성** - 쿼리 편집기를 열고 로드된 데이터 집합을 최적화하기 위해 필요한 작업을 수행합니다.

   * 날씨 관련 열(온도 및 강우량)만 추출합니다.

   * 테이블에서 월 정보를 추출합니다. 이 데이터는 다음 섹션에 설명된 대로 파티션을 만드는 데 유용합니다.

   * 온도 열을 숫자로 변환

   결과 M 표현식은 다음과 같습니다.

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   또한 온도 열의 데이터 형식을 소수로 변경하여 이러한 값을 Power BI에서 플로팅할 수 있도록 합니다.

1. **Azure 분석 파티션 만들기** - Azure Analysis Services에서 파티션을 만들어 데이터 집합을 독립적으로 다른 빈도로 새로 고칠 수 있는 논리 파티션으로 나눕니다. 이 예제에서는 데이터 집합을 가장 최근 달의 데이터와 다른 모든 것으로 나누는 두 개의 파티션을 만듭니다.

   ![분석 서비스 파티션 만들기](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   Azure 분석 서비스에서 다음 두 파티션을 만듭니다.

   * **최근 달** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **역사적인** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Azure 분석 서버에 모델을 배포** - Azure 분석 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **배포를**선택합니다. **배포 서버 속성** 창에 서버 이름을 추가합니다.

   ![Azure 분석 서비스 모델 배포](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **파티션 새로 고침 및 병합 구성** - Azure 분석 서비스를 통해 파티션을 독립적으로 처리할 수 있습니다. **최신 월** 파티션을 최신 데이터로 지속적으로 업데이트하기를 원하므로 새로 고침 간격을 5분으로 설정합니다. 기록 파티션의 데이터를 새로 고칠 필요는 없습니다. 또한 최근 월 파티션을 기록 파티션에 통합하고 새 최신 월 파티션을 만드는 몇 가지 코드를 작성해야 합니다.


## <a name="connect-power-bi-to-analysis-services"></a>전력 BI를 분석 서비스에 연결

1. **Azure 분석 서비스 데이터베이스 커넥터를 사용하여 Azure 분석 서버에 연결** - 라이브 **모드를** 선택하고 다음 스크린샷과 같이 Azure 분석 서비스 인스턴스에 연결합니다.

   ![Azure Analysis Services에서 데이터 가져오기](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **데이터를 로드하고 보고서를 생성** - 이전에 로드한 데이터를 사용하여 온도 및 강우량을 보고하는 차트를 만듭니다. 라이브 연결을 만들므로 이전 단계에서 배포한 Azure Analysis Services 모델의 데이터에 대해 쿼리를 실행해야 합니다. 새 데이터가 Azure Cosmos DB에 로드된 후 5분 이내에 온도 차트가 업데이트됩니다.

   ![데이터 로드 및 보고서 생성](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>다음 단계

* Power BI에 대한 자세한 내용은 [Power BI 시작](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)을 참조하세요.

* [Qlik Sense를 Azure Cosmos DB에 연결하고 데이터 시각화](visualize-qlik-sense.md)