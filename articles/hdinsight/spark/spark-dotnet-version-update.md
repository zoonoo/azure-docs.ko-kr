---
title: HDI에서 .NET for Apache Spark를 버전 v1.0으로 업데이트
description: HDI에서 .NET for Apache Spark 버전을 1.0로 업데이트하는 방법과 이 업데이트가 기존 코드 및 클러스터에 미치는 영향에 관해 알아봅니다.
author: Niharikadutta
ms.author: nidutta
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/05/2021
ms.openlocfilehash: a1602f29a6d0066ec3c99e990532411621652c47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788132"
---
# <a name="updating-net-for-apache-spark-to-version-v10--in-hdinsight"></a>HDInsight에서 .NET for Apache Spark를 버전 v1.0으로 업데이트

이 문서에서는 [.NET for Apache Spark](https://github.com/dotnet/spark)의 첫 번째 주 버전과 해당 버전이 HDInsight 클러스터에서 현재 프로덕션 파이프라인에 미치는 영향에 관해 알아봅니다.

## <a name="about-net-for-apache-spark-version-100"></a>.NET for Apache Spark 버전 1.0.0 정보

이는 .NET for Apache Spark의 첫 번째 [주요 공식 릴리스](https://github.com/dotnet/spark/releases/tag/v1.0.0)이며 다른 기능과 함께 Spark 2.4.x, Spark 3.0.x에 대한 데이터 프레임 API 완성도를 제공합니다. 모든 기능, 향상된 기능, 버그 수정의 전체 목록은 공식 [v1.0.0 릴리스 정보](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md)를 참조하세요.
유의해야 하는 또 다른 중요한 사항은 이 버전이 `Microsoft.Spark`와 `Microsoft.Spark.Worker`의 이전 버전과 호환되지 **않는다는** 것입니다. v1.0.0과 호환되도록 .NET for Apache Spark 애플리케이션을 업그레이드할 계획인 경우 [마이그레이션 가이드](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10)를 확인하세요.

## <a name="using-net-for-apache-spark-v10-in-hdinsight"></a>HDInsight에서 .NET for Apache Spark v1.0 사용

현재 HDI 클러스터는 영향을 받지 않지만(이전과 동일한 버전 포함) 새로 만든 HDI 클러스터는 .NET for Apache Spark의 최신 v1.0.0 버전을 포함합니다. 상황에 따라 수행할 작업

- **이전 HDI 클러스터를 사용하는 경우**: Spark .NET 애플리케이션을 v1.0.0(권장)으로 업그레이드하려면 HDI 클러스터에서 `Microsoft.Spark.Worker` 버전을 업데이트해야 합니다. 자세한 내용은 [HDI 클러스터에서 .NET for Apache Spark 버전 변경 섹션](#changing-net-for-apache-spark-version-on-hdinsight)을 참조하세요.
애플리케이션에서 현재 버전의 .NET for Apache Spark를 업데이트하지 않으려는 경우에는 추가 단계가 필요하지 않습니다.  

- **새 HDI 클러스터를 사용하는 경우**: Spark .NET 애플리케이션을 v1.0.0(권장)으로 업그레이드하는 경우에는 HDI에서 작업자를 변경하는 데 추가 단계가 필요하지 않지만, [마이그레이션 가이드](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10)를 참조하여 코드와 파이프라인을 업데이트하는 데 필요한 단계를 이해해야 합니다.
애플리케이션에서 현재 버전의 .NET for Apache Spark를 변경하지 않으려면 HDI 클러스터의 버전을 v1.0(새 클러스터의 기본값)에서 사용 중인 버전으로 변경해야 합니다. 자세한 내용은 [HDI 클러스터에서 .NET for Apache Spark 버전 변경 섹션](spark-dotnet-version-update.md#changing-net-for-apache-spark-version-on-hdinsight)을 참조하세요.  

## <a name="changing-net-for-apache-spark-version-on-hdinsight"></a>HDInsight에서 .NET for Apache Spark 버전 변경

### <a name="deploy-microsoftsparkworker"></a>Microsoft.Spark.Worker 배포

`Microsoft.Spark.Worker`는 Spark 클러스터의 개별 작업자 노드에 있는 백 엔드 구성 요소입니다. C# UDF(사용자 정의 함수)를 실행하려면 Spark는 .NET CLR를 시작하여 이 UDF를 실행하는 방법을 이해해야 합니다. `Microsoft.Spark.Worker`는 이 기능을 사용하도록 설정하는 Spark에 대한 클래스 컬렉션을 제공합니다. HDI 클러스터에 배포하려는 .NET for Apache Spark 버전에 따라 작업자 버전을 선택합니다.

1. 특정 버전의 Microsoft.Spark.Worker Linux 릴리스를 다운로드합니다. 예를 들어, `.NET for Apache Spark v1.0.0`을 사용하는 경우 [Microsoft.Spark.Worker.netcoreapp3.1.linux-x64-1.0.0.tar.gz](https://github.com/dotnet/spark/releases/tag/v1.0.0)를 다운로드합니다.  

2. [install-worker.sh](https://github.com/dotnet/spark/blob/master/deployment/install-worker.sh) 스크립트를 다운로드하여 1단계에서 다운로드한 작업자 이진 파일을 HDI 클러스터의 모든 작업자 노드에 설치합니다.  

3. 위에서 언급한 파일을 클러스터가 액세스할 수 있는 Azure Storage 계정에 업로드합니다. 자세한 내용은 [.NET for Apache Spark HDI 배포 문서](/dotnet/spark/tutorials/hdinsight-deployment#upload-files-to-azure)를 참조하세요.

4. 스크립트 작업을 사용하여 클러스터의 모든 작업자 노드에서 `install-worker.sh` 스크립트를 실행합니다. 자세한 내용은 [.NET for Apache Spark HDI 배포 문서](/dotnet/spark/tutorials/hdinsight-deployment#run-the-hdinsight-script-action)를 참조하세요.

### <a name="update-your-application-to-use-specific-version"></a>특정 버전을 사용하도록 애플리케이션 업데이트

프로젝트에서 필요한 버전의 [Microsoft.Spark NuGet 패키지](https://www.nuget.org/packages/Microsoft.Spark/)를 선택하여 특정 버전을 사용하도록 .NET for Apache Spark 애플리케이션을 업데이트할 수 있습니다. 애플리케이션을 v1.0.0으로 업데이트하도록 선택하는 경우 위에서 설명한 대로 특정 버전의 릴리스 정보와 [마이그레이션 가이드](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10)를 확인해야 합니다.

## <a name="faqs"></a>FAQ

### <a name="will-my-existing-hdi-cluster-with-version--100-start-failing-with-the-new-release"></a>사용 중인 1.0.0 이전 버전의 기존 HDI 클러스터가 새 릴리스에서 작동하지 않나요?

기존 HDI 클러스터는 .NET for Apache Spark의 동일한 이전 버전을 계속 사용하고 기존 애플리케이션(이전 버전의 Spark .NET 포함)은 영향을 받지 않습니다.

## <a name="next-steps"></a>다음 단계

[HDInsight에 .NET for Apache Spark 애플리케이션 배포](/dotnet/spark/tutorials/hdinsight-deployment)