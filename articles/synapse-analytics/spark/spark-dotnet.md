---
title: Azure 시냅스 분석과 함께 아파치 스파크에 .NET 사용
description: .NET 및 Apache Spark를 사용하여 Azure Synapse Analytics 노트북에서 일괄 처리, 실시간 스트리밍, 기계 학습 및 임시 쿼리 작성에 대해 알아봅니다.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430514"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Azure 시냅스 분석과 함께 아파치 스파크에 .NET 사용

[.NET for Apache Spark는](https://dot.net/spark) Spark에 대한 무료, 오픈 소스 및 크로스 플랫폼 .NET 지원입니다. .NET for Apache Spark는 Spark에 대한 .NET 바인딩을 제공하여 C# 및 F#을 통해 스파크 API에 액세스할 수 있도록 합니다. .NET for Apache Spark를 사용하면 .NET을 사용하여 Spark에 대한 사용자 정의 함수를 작성하고 실행할 수 있습니다. .NET API for Spark를 사용하면 Spark SQL 및 구조화 스트리밍을 포함하여 데이터를 분석하는 데 도움이 되는 Spark의 모든 측면에 액세스할 수 있습니다.

Spark 일괄 처리 작업 정의를 통해 또는 대화형 Azure Synapse Analytics 노트북을 통해 아파치 스파크에 대한 .NET으로 데이터를 분석할 수 있습니다. 이 문서에서는 두 기술을 모두 사용 하 여 Azure Synapse와 아파치 스파크에 대 한 .NET을 사용 하는 방법을 배웁니다. 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>스파크 작업 정의를 사용하여 일괄 처리 작업 제출

Azure Synapse 분석을 사용하여 [Synapse Spark 풀에 대한 아파치 스파크 작업 정의를 만드는](apache-spark-job-definitions.md)방법을 알아보려면 자습서를 방문하십시오. Azure Synapse에 제출할 앱을 패키징하지 않은 경우 다음 단계를 완료합니다.

1. 다음 명령을 실행하여 앱을 게시합니다. *mySparkApp을* 앱경로로 바꿔야 합니다.

   **윈도우에서:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **리눅스에서:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure 시냅스 분석 노트북에서 아파치 스파크에 대한 .NET

새 전자 필기장을 만들 때 비즈니스 논리를 표현하려는 언어 커널을 선택합니다. C#을 포함하여 여러 언어에 대한 커널 지원이 있습니다.

Azure Synapse Analytics 전자 필기장에서 아파치 스파크에 .NET을 사용하려면 **.NET 스파크(C#)를** 커널로 선택하고 노트북을 기존 스파크 풀에 연결합니다.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure 시냅스 분석 노트북에서 아파치 스파크에 대한 .NET 

노트북은 아파치 스파크 파이프라인 및 시나리오에 대해 .NET을 프로토타이핑할 수 있는 훌륭한 옵션입니다. 데이터를 빠르고 효율적으로 작업, 이해, 필터링, 표시 및 시각화할 수 있습니다. 데이터 엔지니어, 데이터 과학자, 비즈니스 분석가 및 기계 학습 엔지니어는 모두 공유된 대화형 문서를 통해 협업할 수 있습니다. 데이터 탐색에서 즉각적인 결과를 볼 수 있으며 동일한 전자 필기장에서 데이터를 시각화할 수 있습니다.

### <a name="how-to-use-notebooks"></a>Notebook을 사용하는 방법

새 전자 필기장을 만들 때 비즈니스 논리를 표현하려는 언어 커널을 선택합니다. C#을 포함하여 여러 언어에 대한 커널 지원이 있습니다. 

Azure Synapse Analytics 전자 필기장에서 아파치 스파크에 .NET을 사용하려면 **.NET 스파크(C#)를** 커널로 선택하고 노트북을 기존 스파크 풀에 연결합니다. 

.NET Spark 노트북은 .NET 대화형 환경을 기반으로 하며 이미 미리 정의된 Spark 세션 변수를 `spark` 사용하여 즉시 .NET for Spark를 사용할 수 있는 대화형 C# 환경을 제공합니다.

### <a name="sparknet-c-kernel-features"></a>Spark.NET C# 커널 기능

다음 기능은 Azure 시냅스 분석 노트북에서 아파치 스파크에 .NET을 사용할 때 사용할 수 있습니다.

* 선언적 HTML: 헤더, 글머리 기호 목록 및 이미지 표시와 같은 HTML 구문을 사용하여 셀에서 출력을 생성합니다.
* 간단한 C# 문(예: 할당, 콘솔로 인쇄, 예외 throw 등).
* 다중 줄 C # 코드 블록 (예 : if 문, foreach 루프, 클래스 정의 등).
* 표준 C# 라이브러리(예: 시스템, LINQ, 열거형 등)에 액세스할 수 있습니다.
* C [# 8.0 언어 기능에](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)대한 지원 .
* 미리 정의된 변수로 '스파크'를 사용하여 아파치 스파크 세션에 액세스할 수 있습니다.
* [아파치 스파크 내에서 실행할 수 있는 .NET 사용자 정의 함수](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql)정의에 대한 지원.
* `XPlot.Plotly` 라이브러리를 사용하여 다양한 차트(예: 선, 막대 또는 히스토그램) 및 레이아웃(예: 단일, 중첩 등)을 사용하여 Spark 작업의 출력을 시각화할 수 있습니다.
* NuGet 패키지를 C# 노트북에 포함할 수 있는 기능입니다.

## <a name="next-steps"></a>다음 단계

* [아파치 스파크 문서용 .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET 인터랙티브](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)