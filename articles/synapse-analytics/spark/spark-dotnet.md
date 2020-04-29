---
title: Azure Synapse Analytics를 사용 하 여 Apache Spark에 .NET 사용
description: .NET 및 Apache Spark를 사용 하 여 Azure Synapse Analytics 노트북에서 일괄 처리, 실시간 스트리밍, 기계 학습 및 임시 쿼리 작성을 수행 하는 방법에 대해 알아봅니다.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430514"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Azure Synapse Analytics를 사용 하 여 Apache Spark에 .NET 사용

[Apache Spark에 대 한 .net](https://dot.net/spark) 은 Spark에 대 한 무료, 오픈 소스 및 플랫폼 간 .net 지원입니다. Apache Spark 용 .NET에서는 c # 및 F #을 통해 Spark Api에 액세스할 수 있도록 Spark에 대 한 .NET 바인딩을 제공 합니다. Apache Spark 용 .NET을 사용 하면 .NET을 사용 하 여 Spark 용 사용자 정의 함수를 작성 하 고 실행할 수 있습니다. Spark 용 .NET Api를 사용 하면 spark SQL 및 구조적 스트리밍을 포함 하 여 데이터를 분석 하는 데 도움이 되는 Spark의 모든 측면에 액세스할 수 있습니다.

Spark batch 작업 정의 또는 대화형 Azure Synapse Analytics 노트북을 통해 Apache Spark 용 .NET을 사용 하 여 데이터를 분석할 수 있습니다. 이 문서에서는 두 가지 방법을 사용 하 여 Azure Synapse에서 Apache Spark .NET을 사용 하는 방법에 대해 알아봅니다. 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Spark 작업 정의를 사용 하 여 batch 작업 제출

Azure Synapse Analytics를 사용 하 여 [Synapse Spark 풀에 대 한 Apache Spark 작업 정의를 만드는](apache-spark-job-definitions.md)방법을 알아보려면 자습서를 참조 하세요. 앱을 패키지 하 여 Azure Synapse에 제출 하지 않은 경우 다음 단계를 완료 합니다.

1. 다음 명령을 실행 하 여 앱을 게시 합니다. *MySparkApp* 를 앱의 경로로 바꾸어야 합니다.

   **Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **Linux:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics 노트북의 Apache Spark에 대 한 .NET

새 노트북을 만들 때 비즈니스 논리를 표현할 언어 커널을 선택 합니다. C #을 비롯 한 여러 언어에 대 한 커널 지원이 있습니다.

Azure Synapse Analytics 노트북에서 Apache Spark .NET을 사용 하려면 커널로 **.Net spark (c #)** 를 선택 하 고 노트북을 기존 Spark 풀에 연결 합니다.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics 노트북의 Apache Spark에 대 한 .NET 

노트북은 Apache Spark 파이프라인과 시나리오를 위해 .NET을 프로토타입 하는 데 유용한 옵션입니다. 데이터를 빠르고 효율적으로 작업 하 고, 이해 하 고, 필터링 하 고, 표시 하 고, 시각화할 수 있습니다. 데이터 엔지니어, 데이터 과학자, 비즈니스 분석가 및 기계 학습 엔지니어는 모두 공유 대화형 문서를 통해 공동 작업을 수행할 수 있습니다. 데이터 탐색의 즉각적인 결과를 볼 수 있으며 동일한 노트북에서 데이터를 시각화할 수 있습니다.

### <a name="how-to-use-notebooks"></a>Notebook을 사용하는 방법

새 노트북을 만들 때 비즈니스 논리를 표현할 언어 커널을 선택 합니다. C #을 비롯 한 여러 언어에 대 한 커널 지원이 있습니다. 

Azure Synapse Analytics 노트북에서 Apache Spark .NET을 사용 하려면 커널로 **.Net spark (c #)** 를 선택 하 고 노트북을 기존 Spark 풀에 연결 합니다. 

.Net Spark 노트북은 .NET interactive 환경을 기반으로 하며, 이미 미리 정의 된 Spark 세션 변수 `spark` 를 사용 하 여 spark에 .net을 사용할 수 있는 기능을 제공 하는 대화형 c # 환경을 제공 합니다.

### <a name="sparknet-c-kernel-features"></a>Spark.NET c # 커널 기능

Azure Synapse Analytics 노트북에서 Apache Spark 용 .NET을 사용 하는 경우 다음 기능을 사용할 수 있습니다.

* 선언적 HTML: 머리글, 글머리 기호 목록 및 이미지 표시와 같은 HTML 구문을 사용 하 여 셀에서 출력을 생성 합니다.
* 간단한 c # 문 (예: 할당, 콘솔에 인쇄, 예외 throw 등).
* 여러 줄 c # 코드 블록 (예: if 문, foreach 루프, 클래스 정의 등).
* 표준 c # 라이브러리 (예: System, LINQ, 열거형 등)에 액세스 합니다.
* [C # 8.0 언어 기능](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)을 지원 합니다.
* Apache Spark 세션에 대 한 액세스를 제공 하기 위해 미리 정의 된 변수로 ' v a r '을 사용 합니다.
* [Apache Spark 내에서 실행할 수 있는 .net 사용자 정의 함수를 정의 하는](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql)기능을 지원 합니다.
* 라이브러리를 사용 하는 `XPlot.Plotly` 다양 한 차트 (예: 선, 가로 막대 또는 히스토그램) 및 레이아웃 (예: 단일, 겹친 등)을 사용 하 여 Spark 작업의 출력을 시각화 하는 기능을 지원 합니다.
* C # 노트북에 NuGet 패키지를 포함 하는 기능.

## <a name="next-steps"></a>다음 단계

* [Apache Spark용 .NET 설명서](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET Interactive](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)