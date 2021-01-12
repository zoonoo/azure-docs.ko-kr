---
title: Apache Spark 용 .NET을 HDI의 버전 v 1.0으로 업데이트 하는 중
description: HDI에서 Apache Spark 버전을 1.0로 업데이트 하는 방법과 기존 코드 및 클러스터에 영향을 주는 방법에 대해 알아봅니다.
author: Niharikadutta
ms.author: nidutta
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/05/2021
ms.openlocfilehash: 4b95e4f164eb2c1d0a881cf9ab14696c3cbed4a7
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122443"
---
# <a name="updating-net-for-apache-spark-to-version-v10--in-hdinsight"></a>Apache Spark 용 .NET을 HDInsight의 버전 v 1.0으로 업데이트

이 문서에서는 [Apache Spark에 대 한 .net](https://github.com/dotnet/spark)의 첫 번째 주 버전 및 HDInsight 클러스터의 현재 프로덕션 파이프라인에 영향을 미칠 수 있는 방법에 대해 설명 합니다.

## <a name="about-net-for-apache-spark-version-100"></a>Apache Spark 버전 1.0.0의 .NET 정보

이는 Apache Spark에 대 한 .NET의 첫 번째 [주요 공식 릴리스](https://github.com/dotnet/spark/releases/tag/v1.0.0) 이며 다른 기능과 함께 spark 2.x. x 및 spark 3.0. x에 대 한 데이터 프레임 API 완전성을 제공 합니다. 모든 기능, 향상 된 기능 및 버그 수정에 대 한 전체 목록은 공식 [v 1.0.0 릴리스 정보](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md)를 참조 하세요.
또 다른 중요 한 사항은이 버전이 및의 이전 버전과 호환 **되지** 않는다는 것입니다 `Microsoft.Spark` `Microsoft.Spark.Worker` . Apache Spark 응용 프로그램에 대 한 .NET을 v 1.0.0과 호환 되도록 업그레이드할 계획인 경우 [마이그레이션 가이드](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) 를 확인 하세요.

## <a name="using-net-for-apache-spark-v10-in-hdinsight"></a>HDInsight에서 Apache Spark v 1.0 용 .NET 사용

현재 HDI 클러스터는 영향을 받지 않습니다. 즉, 이전과 동일한 버전을 유지 하는 동안 새로 만든 HDI 클러스터는 Apache Spark 하기 위해이 최신 v 1.0.0 버전의 .NET을 수행 합니다. 이는 다음을 의미 합니다.

- **이전 hdi 클러스터가 있습니다**. Spark .net 응용 프로그램을 v 1.0.0으로 업그레이드 하려면 (권장) `Microsoft.Spark.Worker` hdi 클러스터의 버전을 업데이트 해야 합니다. 자세한 내용은 [HDI 클러스터에서 Apache Spark에 대 한 .net 버전 변경 섹션](#changing-net-for-apache-spark-version-on-hdinsight)을 참조 하세요.
응용 프로그램에서 Apache Spark에 대 한 현재 버전의 .NET을 업데이트 하지 않으려면 추가 단계가 필요 하지 않습니다.  

- **새 hdi 클러스터가 있습니다**. Spark .net 응용 프로그램을 v 1.0.0으로 업그레이드 하려는 경우 (권장) hdi에서 작업자를 변경 하는 데 필요한 단계는 없지만, 코드 및 파이프라인을 업데이트 하는 데 필요한 단계를 이해 하려면 [마이그레이션 가이드](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) 를 참조 해야 합니다.
응용 프로그램에서 Apache Spark에 대 한 현재 버전의 .NET을 변경 하지 않으려는 경우 HDI 클러스터의 버전을 v 1.0 (새 클러스터의 기본값)에서 사용 중인 버전으로 변경 해야 합니다. 자세한 내용은 [HDI 클러스터에서 Apache Spark에 대 한 .net 버전 변경 섹션](spark-dotnet-version-update.md#changing-net-for-apache-spark-version-on-hdinsight)을 참조 하세요.  

## <a name="changing-net-for-apache-spark-version-on-hdinsight"></a>HDInsight에서 Apache Spark 버전에 대 한 .NET 변경

### <a name="deploy-microsoftsparkworker"></a>Microsoft.Spark.Worker 배포

`Microsoft.Spark.Worker` 는 Spark 클러스터의 개별 작업자 노드에 상주 하는 백 엔드 구성 요소입니다. C # UDF (사용자 정의 함수)를 실행 하려는 경우 Spark는 .NET CLR을 시작 하 여이 UDF를 실행 하는 방법을 이해 해야 합니다. `Microsoft.Spark.Worker` 이 기능을 사용 하는 Spark에 클래스 컬렉션을 제공 합니다. HDI 클러스터에 배포 하려는 Apache Spark에 대 한 .NET 버전에 따라 작업자 버전을 선택 합니다.

1. 특정 버전의 Microsoft Spark. m a m. 예를 들어 원하는 경우 `.NET for Apache Spark v1.0.0` [netcoreapp1.0 linux-x64-1.0.0. release.tar.gz](https://github.com/dotnet/spark/releases/tag/v1.0.0)를 다운로드 하 게 됩니다.  

2. [Install-worker.sh](https://github.com/dotnet/spark/blob/master/deployment/install-worker.sh) 스크립트를 다운로드 하 여 1 단계에서 다운로드 한 작업자 이진을 hdi 클러스터의 모든 작업자 노드에 설치 합니다.  

3. 위에서 언급 한 파일을 클러스터에서 액세스할 수 있는 Azure Storage 계정에 업로드 합니다. 자세한 내용은 [Apache Spark HDI 배포에 대 한 .net 문서](https://docs.microsoft.com/dotnet/spark/tutorials/hdinsight-deployment#upload-files-to-azure) 를 참조할 수 있습니다.

4. `install-worker.sh`스크립트 작업을 사용 하 여 클러스터의 모든 작업자 노드에서 스크립트를 실행 합니다. 자세한 내용은 [Apache Spark HDI 배포에 대 한 .net 문서](https://docs.microsoft.com/dotnet/spark/tutorials/hdinsight-deployment#run-the-hdinsight-script-action) 를 참조 하세요.

### <a name="update-your-application-to-use-specific-version"></a>특정 버전을 사용 하도록 응용 프로그램 업데이트

프로젝트에서 필요한 버전의 [Microsoft Spark NuGet 패키지](https://www.nuget.org/packages/Microsoft.Spark/) 를 선택 하 여 Apache Spark 응용 프로그램용 .net을 업데이트 하 여 특정 버전을 사용할 수 있습니다. 응용 프로그램을 v 1.0.0으로 업데이트 하도록 선택 하는 경우 위에서 설명한 것 처럼 특정 버전 및 [마이그레이션 가이드](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) 의 릴리스 정보를 확인 해야 합니다.

## <a name="faqs"></a>FAQ

### <a name="will-my-existing-hdi-cluster-with-version--100-start-failing-with-the-new-release"></a>기존 HDI 클러스터 버전 < 1.0.0이 새 릴리스로 인해 실패 하는 경우

기존 HDI 클러스터는 Apache Spark에 대해 동일한 이전 버전의 .NET을 계속 사용할 수 있으며 기존 응용 프로그램 (이전 버전의 Spark .NET)은 영향을 받지 않습니다.

## <a name="next-steps"></a>다음 단계

[HDInsight에서 Apache Spark 용 .NET 응용 프로그램 배포](https://docs.microsoft.com/dotnet/spark/tutorials/hdinsight-deployment)
