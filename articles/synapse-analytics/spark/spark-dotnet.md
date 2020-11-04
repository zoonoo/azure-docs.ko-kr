---
title: Apache Spark에 .NET 사용
description: .NET 및 Apache Spark를 사용하여 Azure Synapse Analytics Notebook에서 일괄 처리, 실시간 스트리밍, 기계 학습 및 임시 쿼리 작성을 수행하는 방법에 대해 알아봅니다.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 44c9dbb0eed5e8f7a868832af93a3b841f67f784
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310693"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Azure Synapse Analytics를 사용하여 Apache Spark에 .NET 사용

[.NET for Apache Spark](https://dot.net/spark)는 Spark를 위한 대한 무료 플랫폼 간 오픈 소스 .NET 지원을 제공합니다. 

Spark에 대 한 .NET 바인딩을 제공 하 여 c # 및 F #을 통해 Spark Api에 액세스할 수 있도록 합니다. Apache Spark 용 .NET을 사용 하 여 .NET으로 작성 된 Spark에 대 한 사용자 정의 함수를 작성 하 고 실행할 수도 있습니다. Spark용 .NET API를 사용하면 Spark SQL, Delta Lake, 구조적 스트림을 포함하여 데이터를 분석하는 데 도움이 되는 Spark DataFrames의 모든 요소에 액세스할 수 있습니다.

Spark 일괄 작업 정의 또는 대화형 Azure Synapse Analytics Notebook을 통해 Apache Spark용 .NET을 사용하여 데이터를 분석할 수 있습니다. 이 문서에서는 두 가지 기법을 사용하여 Azure Synapse에서 Apache Spark용 .NET을 사용하는 방법에 대해 알아봅니다.

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Spark 작업 정의를 사용하여 일괄 작업 제출

이 자습서를 방문하여 Azure Synapse Analytics를 사용하여 [Synapse Spark 풀에 대한 Apache Spark 작업 정의를 만드는 방법](apache-spark-job-definitions.md)을 알아보세요. 앱을 패키지 하 여 Azure Synapse에 제출 하지 않은 경우 다음 단계를 완료 합니다.

1. 다음 명령을 실행하여 앱을 게시합니다. *mySparkApp* 은 해당 앱의 경로로 바꾸어야 합니다.

   **Windows에서:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r win-x64
   ```
   
   **Linux:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r ubuntu.16.04-x64
   ```

2. 예를 들어 `publish.zip` 1 단계의 결과로 만들어진 게시 폴더의 콘텐츠를 압축 합니다. 모든 어셈블리는 ZIP 파일의 첫 번째 계층에 있어야 하며 중간 폴더 계층은 없어야 합니다. 즉, 압축을 푸는 경우 `publish.zip` 모든 어셈블리가 현재 작업 디렉터리로 추출 됩니다.

    **Windows:**

[7-Zip](https://www.7-zip.org/) 또는 [WinZip](https://www.winzip.com/)과 같은 추출 프로그램을 사용 하 여 게시 된 모든 이진 파일을 사용 하 여 bin 디렉터리에 파일을 추출 합니다.

     **On Linux:**

     1. Open a bash shell and cd into the bin directory with all the published binaries and run the following command.

       ```bash
       zip -r publish.zip
       ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics Notebook의 Apache Spark용 .NET 

Notebook은 Apache Spark 파이프라인과 시나리오에 맞게 .NET 프로토타입을 제작하는 데 유용한 옵션입니다. 데이터를 빠르고 효율적으로 작업하고, 이해하고, 필터링하고, 표시하고, 시각화할 수 있습니다. 

데이터 엔지니어, 데이터 과학자, 비즈니스 분석가 및 기계 학습 엔지니어는 모두 공유 대화형 문서를 통해 협업을 수행할 수 있습니다. 데이터 탐색의 즉각적인 결과를 볼 수 있으며 동일한 Notebook에서 데이터를 시각화할 수 있습니다.

### <a name="how-to-use-net-for-apache-spark-notebooks"></a>.NET for Apache Spark Notebook을 사용하는 방법

새 Notebook을 만들 때는 비즈니스 논리를 표현할 언어 커널을 선택합니다. 커널 지원은 c #을 비롯 한 여러 언어에서 사용할 수 있습니다.

Azure Synapse Analytics 노트북에서 Apache Spark .NET을 사용 하려면 커널로 **.Net Spark (c #)** 를 선택 하 고 기존 서버를 사용 하지 않는 Apache Spark 풀에 노트북을 연결 합니다.

.Net Spark Notebook은 .NET 대화형 환경을 기반으로 하며, 기본적으로 Spark 세션 변수 `spark`가 미리 정의된 Spark용 .NET을 사용할 수 있는 대화형 C# 환경을 제공합니다.

### <a name="net-for-apache-spark-c-kernel-features"></a>.NET for Apache Spark C# 커널 기능

Azure Synapse Analytics Notebook에서 Apache Spark용 .NET을 사용하는 경우 다음 기능을 사용할 수 있습니다.

* 선언적 HTML: 헤더, 글머리 기호 목록 및 표시 이미지와 같은 HTML 구문을 사용하여 셀에서 출력을 생성합니다.
* 간단한 C# 문(예: 할당, 콘솔에 인쇄, 예외 throw 등)
* 여러 줄로 된 C# 코드 블록(예: if 문, foreach 루프, 클래스 정의 등)
* 표준 C# 라이브러리(예: System, LINQ, 열거형 등)에 대한 액세스
* [C# 8.0 언어 기능](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 지원
* Apache Spark 세션에 대한 액세스를 제공하는 'spark'라는 미리 정의된 변수
* [Apache Spark 내에서 실행할 수 있는 .NET 사용자 정의 함수](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql)를 정의하는 기능 지원
* `XPlot.Plotly` 라이브러리에서 다양한 차트(예: 꺾은선형, 가로 막대형 또는 히스토그램) 및 레이아웃(예: 단일, 오버레이 등)을 사용하여 Spark 작업의 출력을 시각화하는 기능 지원
* NuGet 패키지를 C# Notebook에 포함하는 기능

## <a name="next-steps"></a>다음 단계

* [Apache Spark용 .NET 설명서](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET Interactive](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)
