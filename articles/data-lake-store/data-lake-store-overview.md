---
title: Azure 데이터 레이크 스토리지 Gen1이란 무엇입니까? | Microsoft Docs
description: 데이터 레이크 저장소 Gen1(이전의 Azure Data Lake Store) 및 다른 데이터 저장소에 대해 제공하는 값 개요
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 99384374226fd89cfd672c6b4f851a1743db0764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67118795"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Azure 데이터 레이크 스토리지 Gen1이란 무엇입니까?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1은 빅 데이터 분석 작업을 위한 엔터프라이즈 수준 하이퍼 스케일 리포지토리입니다. Azure 데이터 레이크를 사용하면 작동 및 예비 분석에 대해 한 곳에서 모든 크기, 형식 및 수집 속도의 데이터를 캡처할 수 있습니다.

Data Lake Storage Gen1은 WebHDFS 호환 REST API를 사용하여 Hadoop(HDInsight 클러스터에서 사용 가능)에서 액세스할 수 있습니다. 저장된 데이터에 대한 분석을 사용하도록 설계되었으며 데이터 분석 시나리오의 성능을 위해 조정됩니다. Data Lake Storage Gen1에는 보안, 관리 용이성, 확장성, 안정성 및 가용성등 모든 엔터프라이즈급 기능이 포함되어 있습니다.

![Azure 데이터 레이크](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>주요 기능

Data Lake Storage Gen1의 주요 기능 중 일부는 다음과 같습니다.

### <a name="built-for-hadoop"></a>Hadoop용으로 작성

데이터 레이크 스토리지 Gen1은 하두롭 분산 파일 시스템(HDFS)과 호환되며 Hadoop 생태계와 호환되는 아파치 하두롭 파일 시스템입니다. WebHDFS API를 사용하는 기존 HDInsight 애플리케이션 또는 서비스는 Data Lake Storage Gen1과 쉽게 통합할 수 있습니다. Data Lake Storage Gen1은 또한 애플리케이션에 대한 WebHDFS 호환 REST 인터페이스를 노출합니다.

MapReduce 또는 Hive와 같은 Hadoop 분석 프레임워크를 사용하여 데이터 레이크 스토리지 Gen1에 저장된 데이터를 쉽게 분석할 수 있습니다. Azure HDInsight 클러스터를 프로비전하고 데이터 레이크 저장소 Gen1에 저장된 데이터에 직접 액세스하도록 구성할 수 있습니다.

### <a name="unlimited-storage-petabyte-files"></a>무제한 스토리지, 페타바이트 파일

Data Lake Storage Gen1은 무제한 스토리지를 제공하며 분석을 위해 다양한 데이터를 저장할 수 있습니다. 계정 크기, 파일 크기 또는 데이터 레이크에 저장할 수 있는 데이터의 양에 대한 제한을 부과하지 않습니다. 개별 파일의 크기는 킬로바이트에서 페타바이트까지 다양합니다. 데이터는 여러 복사본을 만들어 지속적으로 저장됩니다. 데이터를 데이터 레이크에 저장할 수 있는 기간에는 제한이 없습니다.

### <a name="performance-tuned-for-big-data-analytics"></a>빅 데이터 분석에 대한 성능 조정

Data Lake Storage Gen1은 대량의 데이터를 쿼리하고 분석하기 위해 엄청난 처리량이 필요한 대규모 분석 시스템을 실행하기 위해 제작되었습니다. 데이터 레이크는 개별 스토리지 서버의 수 이상으로 파일의 일부분을 배포합니다. 데이터 분석을 수행하기 위해 병렬로 파일을 읽을 때 읽기 처리량이 향상됩니다.

### <a name="enterprise-ready-highly-available-and-secure"></a>엔터프라이즈 지원: 가용성 및 보안

Data Lake Storage Gen1은 업계 표준 가용성과 안정성을 제공합니다. 데이터 자산은 모든 예기치 않은 오류로부터 보호하도록 중복 복사본을 만들어 영구적으로 저장됩니다.

또한 Data Lake Storage Gen1은 저장된 데이터에 대한 엔터프라이즈급 보안을 제공합니다. 자세한 내용은 [Azure Data Lake Storage Gen1의 데이터 보안](#DataLakeStoreSecurity)을 참조하세요.

### <a name="all-data"></a>모든 데이터

Data Lake Storage Gen1은 사전 변환 없이 모든 데이터를 기본 형식으로 저장할 수 있습니다. Data Lake Storage Gen1은 데이터가 로드되기 전에 정의되어야 하는 스키마가 필요하지 않으므로 개별 분석 프레임워크가 데이터를 해석하고 분석 시 스키마를 정의할 때까지 그대로 둡니다. 데이터 레이크 스토리지 Gen1은 임의의 크기와 형식의 파일을 저장할 수 있으므로 구조화, 반구조화 및 비정형 데이터를 처리할 수 있습니다.

데이터에 대한 Data Lake Storage Gen1 컨테이너는 기본적으로 폴더 및 파일입니다. SDK, Azure 포털 및 Azure Powershell을 사용하여 저장된 데이터에서 작동합니다. 이러한 인터페이스를 사용하고 적절한 컨테이너를 사용하여 데이터를 저장소에 저장하는 경우 모든 유형의 데이터를 저장할 수 있습니다. Data Lake Storage Gen1은 저장하는 데이터의 형식에 따라 데이터의 특수한 처리를 수행하지 않습니다.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>데이터 보안

Data Lake Storage Gen1은 인증에 Azure Active Directory(Azure AD)를 사용하고 ACL(액세스 제어 목록)을 사용하여 데이터에 대한 액세스를 관리합니다.

| 기능 | 설명 |
| --- | --- |
| 인증 |데이터 레이크 저장소 Gen1은 데이터 레이크 저장소 Gen1에 저장된 모든 데이터에 대한 ID 및 액세스 관리를 위해 Azure AD와 통합됩니다. 통합으로 인해 Data Lake Storage Gen1은 다단계 인증, 조건부 액세스, 역할 기반 액세스 제어, 응용 프로그램 사용 모니터링, 보안 모니터링 및 경고 등과 같은 모든 Azure AD 기능의 이점을 누릴 수 있습니다. Data Lake Storage Gen1은 REST 인터페이스에서 인증을 위한 OAuth 2.0 프로토콜을 지원합니다. [데이터 레이크 스토리지 Gen1 인증을](data-lakes-store-authentication-using-azure-active-directory.md)참조하십시오.|
| Access Control |Data Lake Storage Gen1은 WebHDFS 프로토콜에 의해 노출되는 POSIX 스타일 권한을 지원하여 액세스 제어를 제공합니다. 루트 폴더, 하위 폴더 및 개별 파일에 ACL을 활성화할 수 있습니다. 데이터 레이크 스토리지 Gen1의 컨텍스트에서 ACL이 작동하는 방식에 대한 자세한 내용은 [데이터 레이크 스토리지 Gen1의 액세스 제어를](data-lake-store-access-control.md)참조하십시오. |
| 암호화 |데이터 레이크 스토리지 Gen1은 계정에 저장된 데이터에 대한 암호화도 제공합니다. Data Lake Storage Gen1 계정을 만드는 동안 암호화 설정을 지정합니다. 암호화된 데이터 또는 암호화 없음을 선택할 수 있습니다. 자세한 내용은 [Data Lake Storage Gen1의 암호화](data-lake-store-encryption.md)를 참조하세요. 암호화 관련 구성을 제공하는 방법에 대한 지침은 [Azure 포털을 사용하여 데이터 레이크 저장소 Gen1을 시작하기](data-lake-store-get-started-portal.md)를 참조하십시오. |

Data Lake Storage Gen1의 데이터 보안 방법에 대한 지침은 [Azure Data Lake Storage Gen1의 데이터 보안](data-lake-store-secure-data.md)을 참조하세요.

## <a name="application-compatibility"></a>애플리케이션 호환성

Data Lake Storage Gen1은 Hadoop 에코시스템의 오픈 소스 구성 요소 대부분과 호환됩니다. 또한 다른 Azure 서비스와잘 통합됩니다. 오픈 소스 구성 요소 및 기타 Azure 서비스에서 Data Lake Storage Gen1을 사용하는 방법에 대해 자세히 알아보려면 다음 링크를 사용하십시오.

- Data Lake Storage Gen1과 상호 운용 가능한 오픈 소스 애플리케이션 목록은 [Azure Data Lake Storage Gen1과 호환되는 애플리케이션 및 서비스](data-lake-store-compatible-oss-other-applications.md)를 참조하세요.
- [다른 Azure 서비스와 통합을](data-lake-store-integrate-with-other-services.md) 참조하여 데이터 레이크 저장소 Gen1을 다른 Azure 서비스와 사용하는 방법을 이해하여 광범위한 시나리오를 활성화합니다.
- [Data Lake Storage Gen1을 사용하는 시나리오](data-lake-store-data-scenarios.md)를 참조하여 데이터 수집, 데이터 처리, 데이터 다운로드 및 데이터 시각화와 같은 시나리오에서 Data Lake Storage Gen1을 사용하는 방법을 알아봅니다.

## <a name="data-lake-storage-gen1-file-system"></a>데이터 레이크 스토리지 Gen1 파일 시스템

데이터 레이크 스토리지 Gen1은 하두프 환경에서 파일 시스템 AzureDataLakeFilesystem(adl://)을 통해 액세스할 수 있습니다(HDInsight 클러스터에서 사용 가능). adl:// 사용하는 응용 프로그램 및 서비스는 WebHDFS에서 현재 사용할 수 없는 추가 성능 최적화를 활용할 수 있습니다. 따라서 Data Lake Storage Gen1은 WebHDFS API를 계속 사용하여 adl:// 사용하는 권장 옵션으로 최상의 성능을 활용하거나 기존 코드를 유지할 수 있는 유연성을 제공합니다. Azure HDInsight는 Data Lake Storage Gen1에서 최상의 성능을 제공하도록 AzureDataLakeFilesystem을 완벽하게 활용합니다.

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`을 사용하여 Data Lake Storage Gen1의 데이터에 액세스할 수 있습니다. Data Lake Storage Gen1의 데이터에 액세스하는 방법에 대한 자세한 내용은 [저장된 데이터의 속성 보기를](data-lake-store-get-started-portal.md#properties)참조하십시오.

## <a name="next-steps"></a>다음 단계

- [Azure 포털을 사용하여 데이터 레이크 스토리지 Gen1 시작](data-lake-store-get-started-portal.md)
- [.NET SDK를 사용하여 데이터 레이크 스토리지 Gen1시작](data-lake-store-get-started-net-sdk.md)
- [Data Lake Storage Gen1에서 Azure HDInsight 사용](data-lake-store-hdinsight-hadoop-use-portal.md)