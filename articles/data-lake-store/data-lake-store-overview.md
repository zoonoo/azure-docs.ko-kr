---
title: Azure Data Lake Storage Gen1 개요 | Microsoft Docs
description: Data Lake Storage Gen1(이전의 Azure Data Lake Store)이란 무엇이고 다른 데이터 저장소에 비교해 어떠한 가치가 있는지를 이해합니다.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: b733a0bec20c7abae5df41acd74284bdf75bca2c
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124136"
---
# <a name="overview-of-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 개요

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1은 빅 데이터 분석 작업을 위한 엔터프라이즈 수준 하이퍼 스케일 리포지토리입니다. Azure 데이터 레이크를 사용하면 작동 및 예비 분석에 대해 한 곳에서 모든 크기, 형식 및 수집 속도의 데이터를 캡처할 수 있습니다.

> [!TIP]
> Data Lake Storage Gen1 서비스 탐색을 시작하려면 [Data Lake Storage Gen1 학습 경로](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/)를 사용하세요.
> 
> 

Data Lake Storage Gen1은 WebHDFS 호환 REST API를 사용하여 Hadoop(HDInsight 클러스터에서 사용 가능)에서 액세스할 수 있습니다. 저장된 데이터에 대한 분석을 사용하도록 특별히 설계되었으며 데이터 분석 시나리오에 대한 성능을 위해 조정됩니다. 기본적으로 실제 엔터프라이즈 사용 사례에 필수적인 모든 엔터프라이즈급 기능-보안, 관리 효율성, 확장성, 안정성 및 가용성-을 포함합니다.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Data Lake Storage Gen1의 주요 기능 중 일부는 다음과 같습니다.

### <a name="built-for-hadoop"></a>Hadoop용으로 작성
Data Lake Storage Gen1은 HDFS(Hadoop 분산 파일 시스템)와 호환되는 Apache Hadoop 파일 시스템이며 Hadoop 에코시스템과 함께 작동됩니다.  WebHDFS API를 사용하는 기존 HDInsight 애플리케이션 또는 서비스는 Data Lake Storage Gen1과 쉽게 통합할 수 있습니다. Data Lake Storage Gen1은 또한 애플리케이션에 대한 WebHDFS 호환 REST 인터페이스를 노출합니다.

Data Lake Storage Gen1에 저장된 데이터는 MapReduce 또는 Hive와 같은 Hadoop 분석 프레임워크를 사용하여 쉽게 분석될 수 있습니다. Microsoft Azure HDInsight 클러스터는 Data Lake Storage Gen1에 저장된 데이터에 직접 액세스하도록 프로비전되고 구성될 수 있습니다.

### <a name="unlimited-storage-petabyte-files"></a>무제한 저장소, 페타바이트 파일
Data Lake Storage Gen1은 무제한 스토리지를 제공하며 분석에 대한 다양한 데이터를 저장하는 데 적합합니다. 데이터 레이크에 저장될 수 있는 계정 크기, 파일 크기 또는 데이터 양에 어떠한 제한도 적용하지 않습니다. 개별 파일의 범위는 모든 종류의 데이터를 저장하는데 적합하도록 킬로바이트에서 페타바이트까지입니다. 데이터는 여러 복사본을 만들어 영구적으로 저장되며 데이터가 데이터 레이크에 저장될 수 있는 기간에 제한이 없습니다.

### <a name="performance-tuned-for-big-data-analytics"></a>빅 데이터 분석에 대한 성능 조정
Data Lake Storage Gen1은 많은 양의 데이터에 대한 쿼리 및 분석을 위해 대규모 처리 능력이 필요한 대규모 분석 시스템을 실행할 수 있도록 제작되었습니다. 데이터 레이크는 개별 저장소 서버의 수 이상으로 파일의 일부분을 배포합니다. 데이터 분석을 수행하기 위해 병렬로 파일을 읽을 때 읽기 처리량이 향상됩니다.

### <a name="enterprise-ready-highly-available-and-secure"></a>엔터프라이즈 지원: 고가용성 및 보안
Data Lake Storage Gen1은 업계 표준 가용성과 안정성을 제공합니다. 데이터 자산은 모든 예기치 않은 오류로부터 보호하도록 중복 복사본을 만들어 영구적으로 저장됩니다. 기업에서는 기존 데이터 플랫폼의 중요한 부분으로 솔루션에서 Data Lake Storage Gen1을 사용할 수 있습니다.

또한 Data Lake Storage Gen1은 저장된 데이터에 대한 엔터프라이즈급 보안을 제공합니다. 자세한 내용은 [Azure Data Lake Storage Gen1의 데이터 보안](#DataLakeStoreSecurity)을 참조하세요.

### <a name="all-data"></a>모든 데이터
Data Lake Storage Gen1은 사전 변환 없이 모든 데이터를 원시 형식으로 그대로 저장할 수 있습니다. Data Lake Storage Gen1은 데이터가 로드되기 전에 정의되어야 하는 스키마가 필요하지 않으므로 개별 분석 프레임워크가 데이터를 해석하고 분석 시 스키마를 정의할 때까지 그대로 둡니다. 임의 크기 및 형식의 파일을 저장 가능한 것은 Data Lake Storage Gen1이 구조화, 반구조화 및 비구조화된 데이터를 처리할 수 있도록 합니다.

데이터에 대한 Data Lake Storage Gen1 컨테이너는 기본적으로 폴더 및 파일입니다. SDK, Azure Portal 및 Azure Powershell을 사용하여 저장된 데이터에서 작동합니다. 이러한 인터페이스 및 적절한 컨테이너를 사용하여 저장소에 데이터를 저장하는 한 모든 종류의 데이터를 저장할 수 있습니다. Data Lake Storage Gen1은 저장하는 데이터의 형식에 따라 데이터의 특수한 처리를 수행하지 않습니다.

## <a name="DataLakeStoreSecurity"></a>Data Lake Storage Gen1의 데이터 보호
Data Lake Storage Gen1은 인증 및 ACL(액세스 제어 목록)을 위해 Azure Active Directory를 사용하여 데이터에 대한 액세스를 관리합니다.

| 기능 | 설명 |
| --- | --- |
| 인증 |Data Lake Storage Gen1은 Data Lake Storage Gen1에 저장된 모든 데이터에 대한 ID 및 액세스 관리를 위해 Azure Active Directory(AAD)와 통합합니다. 통합의 결과로 Data Lake Storage Gen1은 다단계 인증, 조건부 액세스, 역할 기반 액세스 제어, 애플리케이션 사용 모니터링, 보안 모니터링 및 경고 등을 포함한 모든 AAD 기능의 이점을 얻습니다. Data Lake Storage Gen1은 REST 인터페이스에서 인증을 위한 OAuth 2.0 프로토콜을 지원합니다. [Data Lake Storage Gen1 인증](data-lakes-store-authentication-using-azure-active-directory.md)을 참조하세요.|
| Access Control |Data Lake Storage Gen1은 WebHDFS 프로토콜에 의해 노출되는 POSIX 스타일 권한을 지원하여 액세스 제어를 제공합니다. ACL은 루트 폴더, 하위 폴더 및 개별 파일에서도 사용할 수 있습니다. Data Lake Storage Gen1의 컨텍스트에서 ACL 작동 방법에 대한 자세한 내용은 [Data Lake Storage Gen1의 액세스 제어](data-lake-store-access-control.md)를 참조하세요. |
| 암호화 |또한 Data Lake Storage Gen1은 계정에 저장된 데이터에 대한 암호화를 제공합니다. Data Lake Storage Gen1 계정을 만드는 동안 암호화 설정을 지정합니다. 암호화된 데이터 또는 암호화 없음을 선택할 수 있습니다. 자세한 내용은 [Data Lake Storage Gen1의 암호화](data-lake-store-encryption.md)를 참조하세요. 암호화 관련 구성을 제공하는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)을 참조하세요. |

Data Lake Storage Gen1의 데이터를 보호하는 방법에 대해 자세히 알아보려면 아래 링크를 따라갑니다.

* Data Lake Storage Gen1의 데이터 보안 방법에 대한 지침은 [Azure Data Lake Storage Gen1의 데이터 보안](data-lake-store-secure-data.md)을 참조하세요.
* 비디오를 선호하십니까? Data Lake Storage Gen1에 저장된 데이터를 보호하는 방법을 알려주는 [이 비디오를 참고하세요](https://mix.office.com/watch/1q2mgzh9nn5lx).

## <a name="applications-compatible-with-data-lake-storage-gen1"></a>Data Lake Storage Gen1과 호환되는 애플리케이션
Data Lake Storage Gen1은 Hadoop 에코시스템의 오픈 소스 구성 요소 대부분과 호환됩니다. 또한 다른 Azure 서비스와 원활하게 통합됩니다. 따라서 Data Lake Storage Gen1은 데이터 스토리지 요구 사항에 맞는 완벽한 옵션입니다. 다른 Azure 서비스뿐만 아니라 오픈 소스 구성 요소와 함께 Data Lake Storage Gen1을 사용할 수 있는 방법을 자세히 알아보려면 아래 링크를 따라갑니다.

* Data Lake Storage Gen1과 상호 운용 가능한 오픈 소스 애플리케이션 목록은 [Azure Data Lake Storage Gen1과 호환되는 애플리케이션 및 서비스](data-lake-store-compatible-oss-other-applications.md)를 참조하세요.
* Data Lake Storage Gen1을 다른 Azure 서비스와 사용하여 광범위한 시나리오를 활성화할 방법을 이해하려면 [다른 Azure 서비스와 통합](data-lake-store-integrate-with-other-services.md)을 참조하세요.
* [Data Lake Storage Gen1을 사용하는 시나리오](data-lake-store-data-scenarios.md)를 참조하여 데이터 수집, 데이터 처리, 데이터 다운로드 및 데이터 시각화와 같은 시나리오에서 Data Lake Storage Gen1을 사용하는 방법을 알아봅니다.

## <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 파일 시스템(adl://)이란 무엇인가요?
Hadoop 환경에서 새로운 파일 시스템인 AzureDataLakeFilesystem(adl://)을 통해 Data Lake Storage Gen1에 액세스할 수 있습니다(HDInsight 클러스터에서 사용 가능). adl://를 사용하는 애플리케이션 및 서비스는 현재 WebHDFS에서 사용할 수 없는 추가 성능 최적화의 장점을 활용할 수 있습니다. 그 결과 Data Lake Storage Gen1은 권장되는 옵션의 adl://를 사용하여 최상의 성능을 이용하거나 WebHDFS API를 계속해서 직접 사용하여 기존 코드를 유지하도록 유연성을 제공합니다. Azure HDInsight는 Data Lake Storage Gen1에서 최상의 성능을 제공하도록 AzureDataLakeFilesystem을 완벽하게 활용합니다.

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`을 사용하여 Data Lake Storage Gen1의 데이터에 액세스할 수 있습니다. Data Lake Storage Gen1의 데이터에 액세스하는 방법에 대한 자세한 내용은 [저장된 데이터의 속성 보기](data-lake-store-get-started-portal.md#properties)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Portal을 사용하여 Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)
* [.NET SDK를 사용하여 Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-net-sdk.md)
* [Data Lake Storage Gen1에서 Azure HDInsight 사용](data-lake-store-hdinsight-hadoop-use-portal.md)