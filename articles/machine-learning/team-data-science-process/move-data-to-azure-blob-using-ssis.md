---
title: SSIS 커넥터를 사용하여 Blob 스토리지 데이터 이동 - Team Data Science Process
description: Azure용 SQL Server 통합 서비스 기능 팩을 사용하여 Azure Blob 저장소로 또는 Azure Blob 저장소에서 데이터를 이동하는 방법을 알아봅니다.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 77bfd9d5bcae7bedd673354e32464d5f59bdc9b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720874"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>SSIS 커넥터를 사용하여 Azure Blob Storage의 데이터 이동
[Azure용 SQL Server Integration Services 기능 팩](https://msdn.microsoft.com/library/mt146770.aspx) 에서는 Azure에 연결하고, Azure와 온-프레미스 데이터 원본 간에 데이터를 전송하며, Azure에 저장된 데이터를 처리하는 구성 요소를 제공합니다.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

고객이 온-프레미스 데이터를 클라우드로 이동하면 모든 Azure 서비스에서 데이터에 액세스하여 Azure 기술 제품군의 모든 권한을 활용할 수 있습니다. 데이터는 Azure 기계 학습 또는 HDInsight 클러스터에서 나중에 사용될 수 있습니다.

이러한 Azure 리소스를 사용하는 예는 [SQL](sql-walkthrough.md) 및 [HDInsight](hive-walkthrough.md) 연습에 있습니다.

SSIS를 사용하여 하이브리드 데이터 통합 시나리오에서 일반적인 비즈니스 요구 사항을 충족하는 정식 시나리오에 대한 자세한 내용은 [Azure용 SQL Server Integration Services 통합 팩으로 더 많은 작업 수행](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) 블로그를 참조하세요.

> [!NOTE]
> Azure Blob Storage에 대한 전체 소개 내용은 [Azure Blob 기본 사항](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) 및 [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx)를 참조하세요.
> 
> 

## <a name="prerequisites"></a>사전 요구 사항
이 문서에서 설명하는 작업을 수행하려면 Azure 구독 및 Azure Storage 계정이 설정되어 있어야 합니다. 데이터를 업로드하거나 다운로드하려면 Azure Storage 계정 이름과 계정 키가 필요합니다.

* **Azure 구독**을 설정하려면 [1개월 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **저장소 계정을** 만들고 계정 및 주요 정보를 가져오는 방법에 대한 지침은 Azure Storage [계정 정보를](../../storage/common/storage-create-storage-account.md)참조하십시오.

**SSIS 커넥터**를 사용하려면 다음을 다운로드해야 합니다.

* **SQL Server 2014 또는 2016 Standard 이상**: 설치 파일에 SQL Server Integration Services가 포함되어 있습니다.
* **Azure용 Microsoft SQL Server 2014 또는 2016 통합 서비스 기능 팩**: 이러한 커넥터는 SQL Server [2014 통합 서비스](https://www.microsoft.com/download/details.aspx?id=47366) 및 SQL Server [2016 통합 서비스](https://www.microsoft.com/download/details.aspx?id=49492) 페이지에서 각각 다운로드할 수 있습니다.

> [!NOTE]
> SSIS는 SQL Server와 함께 설치되지만 Express 버전에는 포함되어 있지 않습니다. 다양한 버전의 SQL Server에 포함된 애플리케이션에 대한 자세한 내용은 [SQL Server 버전](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)을 참조하세요.
> 
> 

SSIS에 대한 교육 자료는 [SSIS에 대한 실습 교육](https://www.microsoft.com/sql-server/training-certification)

SISS를 사용하여 간단한 ETL(추출, 변환 및 로드) 패키지를 빌드하는 방법에 대한 자세한 내용은 [SSIS 자습서: 간단한 ETL 패키지 만들기](https://msdn.microsoft.com/library/ms169917.aspx)를 참조하세요.

## <a name="download-nyc-taxi-dataset"></a>NYC Taxi 데이터 세트 다운로드
여기에 설명된 예제에서는 공개적으로 제공되는 데이터 세트인 [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) 데이터 세트를 사용합니다. 이 데이터 세트는 2013년 뉴욕 시의 1억 7,300만 건에 달하는 택시 승차 기록으로 구성됩니다. 데이터는 여정 정보 데이터와 요금 데이터의 두 종류가 있습니다. 매월 파일이 있으므로 24 개의 파일이 있으며 각각 압축되지 않은 약 2 GB입니다.

## <a name="upload-data-to-azure-blob-storage"></a>Azure File Storage는 Windows 및 기타 운영 체제에 대해 표준 SMB 2.1 프로토콜을 사용하므로, 응용 프로그램은 파일 공유 열기, 액세스 및 관리에 대해 익숙한 FileSystem API를 계속 사용할 수 있습니다.
SSIS 기능 팩을 사용하여 온-프레미스에서 Azure Blob 저장소로 데이터를 이동하려면 여기에 표시된 [**Azure Blob 업로드 작업의**](https://msdn.microsoft.com/library/mt146776.aspx)인스턴스를 사용합니다.

![configure-data-science-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

작업에 사용되는 매개 변수는 다음과 같습니다.

| 필드 | 설명 |
| --- | --- |
| **AzureStorageConnection** |기존 Azure 저장소 연결 관리자를 지정하거나 Blob 파일이 호스팅되는 위치를 가리키는 Azure 저장소 계정을 참조하는 새 저장소 관리자를 만듭니다. |
| **BlobContainer** |업로드된 파일을 Blob으로 보유하는 Blob 컨테이너의 이름을 지정합니다. |
| **BlobDirectory** |업로드한 파일이 블록 blob으로 저장되는 blob 디렉터리를 지정합니다. blob 디렉터리는 가상 계층 구조입니다. blob가 이미 있는 경우 바뀝니다. |
| **LocalDirectory** |업로드할 파일이 포함된 로컬 디렉터리를 지정합니다. |
| **파일** |지정된 이름 패턴의 파일을 선택할 이름 필터를 지정합니다. 예를 들어 MySheet\*.xls\*는 MySheet001.xls 및 MySheetABC.xlsx와 같은 파일을 포함합니다. |
| **TimeRangeFrom/TimeRangeTo** |시간 범위 필터를 지정합니다. *TimeRangeFrom*에서 *TimeRangeTo* 사이에 수정된 파일이 포함됩니다. |

> [!NOTE]
> 전송을 시도하기 전에 **AzureStorageConnection** 자격 증명이 올바르고 **BlobContainer**가 있어야 합니다.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Azure Blob Storage에서 데이터 다운로드
SSIS를 사용하여 Azure Blob Storage에서 온-프레미스 스토리지로 데이터를 다운로드하려면 [Azure Blob 다운로드 작업](https://msdn.microsoft.com/library/mt146779.aspx)의 인스턴스를 사용합니다.

## <a name="more-advanced-ssis-azure-scenarios"></a>고급 SSIS-Azure 시나리오
SSIS 기능 팩을 사용하면 패키징 작업을 통해 보다 복잡한 흐름을 처리할 수 있습니다. 예를 들어 blob 데이터를 HDInsight 클러스터에 직접 공급하여 해당 출력을 blob로 다시 다운로드한 다음 온-프레미스 스토리지에 다운로드할 수 있습니다. SSIS는 추가 SSIS 커넥터를 사용하여 HDInsight 클러스터에서 Hive 및 Pig 작업을 실행할 수 있습니다.

* SSIS를 사용하여 Azure HDInsight 클러스터에서 Hive 스크립트를 실행하려면 [Azure HDInsight Hive 작업](https://msdn.microsoft.com/library/mt146771.aspx)을 사용합니다.
* SSIS를 사용하여 Azure HDInsight 클러스터에서 Pig 스크립트를 실행하려면 [Azure HDInsight Pig 작업](https://msdn.microsoft.com/library/mt146781.aspx)을 사용합니다.

