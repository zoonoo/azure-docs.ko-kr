---
title: Azure 데이터 공유에서 지원되는 데이터 저장소
description: Azure 데이터 공유를 사용하도록 지원되는 데이터 저장소에 대해 알아봅니다.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 624bb45de3e2ff184326949611d437f71f3e2def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501814"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure 데이터 공유에서 지원되는 데이터 저장소

Azure 데이터 공유는 서로 다른 데이터 저장소에서 공유하는 기능을 포함하여 개방적이고 유연한 데이터 공유를 제공합니다. 데이터 공급자는 한 가지 유형의 데이터 저장소에서 데이터를 공유할 수 있으며, 데이터 소비자는 데이터를 수신할 데이터 저장소를 선택할 수 있습니다. 

이 문서에서는 Azure 데이터 공유에서 지원되는 다양한 Azure 데이터 저장소 집합에 대해 알아봅니다. 또한 데이터 공급자와 데이터 소비자가 활용할 수 있는 데이터 저장소의 조합에 대한 정보를 찾을 수도 있습니다. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Azure 데이터 공유에서 지원되는 데이터 저장소는 무엇입니까? 

아래 표는 Azure 데이터 공유에 대해 지원되는 데이터 원본에 대해 자세히 설명합니다. 

| 데이터 저장소 | 스냅샷 기반 공유 | 장소 내 공유 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 스토리지 |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |공개 미리 보기 | |
| Azure 시냅스 분석(이전의 Azure SQL DW) |공개 미리 보기 | |
| Azure Data Explorer | |공개 미리 보기 |

## <a name="data-store-support-matrix"></a>데이터 저장소 지원 매트릭스

Azure Data Share는 데이터 저장소를 결정할 때 데이터 소비자에게 데이터를 허용할 때 유연성을 제공합니다. 예를 들어 Azure SQL Database에서 공유되는 데이터를 Azure Data Lake Store Gen2, Azure SQL 데이터베이스 또는 Azure 시냅스 분석으로 수신할 수 있습니다. 고객은 수신된 데이터 공유를 구성할 때 데이터를 수신할 형식을 선택할 수 있습니다. 

아래 표는 데이터 소비자가 데이터 공유를 수락하고 구성할 때 가지는 선택 사항과 다양한 조합을 자세히 설명합니다. 데이터 집합 매핑을 구성하는 방법에 대한 자세한 내용은 [데이터 집합 매핑을 구성하는 방법을 참조하세요.](how-to-configure-mapping.md)

|  | Azure Blob Storage | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL Database | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 스토리지 | ✓ || ✓|
| Azure Data Lake Storage Gen1 | ✓ | | ✓|
| Azure Data Lake Storage Gen2 | ✓ | | ✓|
| Azure SQL Database | ✓ | | ✓| ✓| ✓|
| Azure 시냅스 분석(이전의 Azure SQL DW) | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>저장소 계정에서 공유
Azure 데이터 공유는 Azure Data Lake Gen1 및 Azure Data Lake Gen2의 파일, 폴더 및 파일 시스템 공유를 지원합니다. 또한 Azure Blob 저장소에서 Blob, 폴더 및 컨테이너의 공유를 지원합니다. 현재 블록 Blob만 지원됩니다. 폴더가 스냅샷 기반 공유에서 공유되는 경우 데이터 소비자는 공유 데이터의 전체 복사본을 만들거나 증분 스냅숏 기능을 활용하여 새 파일또는 업데이트된 파일만 복사할 수 있습니다. 이름이 같은 기존 파일은 덮어씁니다.

## <a name="share-from-a-sql-based-source"></a>SQL 기반 소스에서 공유
Azure 데이터 공유는 Azure SQL Database 및 Azure 시냅스 분석(이전의 Azure SQL DW)에서 테이블 또는 뷰의 공유를 지원합니다. 데이터 소비자는 데이터를 Azure Data Lake Store Gen2 또는 Azure Blob 저장소에 csv 또는 마루 파일로 수락하도록 선택할 수 있습니다. 기본적으로 파일 형식은 csv입니다. 데이터 소비자는 원하는 경우 마루 형식으로 데이터를 수신하도록 선택할 수 있습니다. 이 작업은 데이터를 수신할 때 데이터 집합 매핑 설정에서 수행할 수 있습니다. 

Azure Data Lake Store Gen2 또는 Azure Blob 저장소에 데이터를 수락할 때 전체 스냅숏은 대상 파일의 내용을 덮어씁니다. 

데이터 소비자는 원하는 테이블로 데이터를 수신하도록 선택할 수 있습니다. 이 시나리오에서 대상 테이블이 아직 없는 경우 Azure Data Share는 원본 스키마를 사용하여 SQL 테이블을 만듭니다. 대상 테이블이 이미 같은 이름으로 있는 경우 삭제되고 최신 전체 스냅숏으로 덮어씁니다. 대상 테이블을 매핑할 때 대체 스키마와 테이블 이름을 지정할 수 있습니다. 증분 스냅숏은 현재 지원되지 않습니다. 

SQL 기반 원본에서 공유하는 경우 방화벽 규칙 및 사용 권한과 관련된 필수 조건이 있습니다. 자세한 내용은 [데이터 공유](share-your-data.md) 자습서의 필수 구성 조건 섹션을 참조하십시오.

## <a name="share-from-azure-data-explorer"></a>Azure Data Explorer에서 공유
Azure 데이터 공유는 Azure 데이터 탐색기 클러스터에서 데이터베이스를 제자리에 공유하는 기능을 지원합니다. 데이터 공급자는 데이터베이스 또는 클러스터 수준에서 공유할 수 있습니다. 데이터베이스 수준에서 공유하면 데이터 소비자는 데이터 공급자가 공유하는 특정 데이터베이스에만 액세스할 수 있습니다. 클러스터 수준에서 공유하면 데이터 소비자는 데이터 공급자가 만든 향후 데이터베이스를 포함하여 공급자의 클러스터에서 모든 데이터베이스에 액세스할 수 있습니다.

공유 데이터베이스에 액세스하려면 데이터 소비자가 자체 Azure Data Explorer 클러스터를 가져야 합니다. 데이터 소비자의 Azure 데이터 탐색기 클러스터는 데이터 공급자의 Azure 데이터 탐색기 클러스터와 동일한 Azure 데이터 센터에서 찾아야 합니다. 공유 관계가 설정되면 Azure Data Share는 공급자와 소비자의 Azure 데이터 탐색기 클러스터 간에 기호 링크를 만듭니다.

Azure Data Explorer는 일괄 처리 및 스트리밍이라는 두 가지 데이터 수집 모드를 지원합니다. 공유 데이터베이스의 일괄 처리에서 받은 데이터는 데이터 소비자 측에서 몇 초에서 몇 분 사이에 나타납니다. 스트리밍에서 수신된 데이터는 데이터 소비자 측에 표시되는 데 최대 24시간이 걸릴 수 있습니다. 

## <a name="next-steps"></a>다음 단계

데이터 공유를 시작하는 방법을 알아보려면 [데이터 공유](share-your-data.md) 자습서로 계속 진행하세요.
