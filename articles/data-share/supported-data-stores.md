---
title: Azure 데이터 공유에서 지원 되는 데이터 저장소
description: Azure 데이터 공유 사용에 대해 지원 되는 데이터 저장소에 대해 알아봅니다.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 56103ed89d2e7813fd60bc50ecca7271f5421a4a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438683"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure 데이터 공유에서 지원 되는 데이터 저장소

Azure 데이터 공유는 다양 한 데이터 저장소에서 공유 하는 기능을 포함 하 여 유연한 개방형 데이터 공유 기능을 제공 합니다. 데이터 공급자는 특정 유형의 데이터 저장소에서 데이터를 공유할 수 있으며 데이터 소비자는 데이터를 받을 데이터 저장소를 선택할 수 있습니다. 

이 문서에서는 Azure 데이터 공유에서 지원 되는 다양 한 Azure 데이터 저장소 집합에 대해 알아봅니다. 데이터 공급자 및 데이터 소비자가 활용할 수 있는 데이터 저장소의 조합에 대 한 정보를 찾을 수도 있습니다. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Azure 데이터 공유에서 지원 되는 데이터 저장소는 무엇입니까? 

아래 표에서는 Azure 데이터 공유에 대해 지원 되는 데이터 원본을 자세히 설명 합니다. 

| 데이터 저장소 | 스냅숏 기반 공유 | 현재 항목 공유 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |공개 미리 보기 | |
| Azure Synapse Analytics (이전의 Azure SQL DW) |공개 미리 보기 | |
| Azure Data Explorer | |[제한 된 미리 보기](https://aka.ms/azuredatasharepreviewsignup) |

## <a name="data-store-support-matrix"></a>데이터 저장소 지원 매트릭스

Azure 데이터 공유는 데이터 저장소를 결정할 때 데이터 소비자에 게 데이터를 허용할 수 있는 유연성을 제공 합니다. 예를 들어 Azure SQL Database에서 공유 되는 데이터를 Azure Data Lake Store Gen2, Azure SQL Database 또는 Azure Synapse Analytics로 받을 수 있습니다. 고객은 수신 된 데이터 공유를 구성할 때 데이터를 받을 형식을 선택할 수 있습니다. 

아래 표에서는 데이터 소비자가 데이터 공유를 수락 하 고 구성할 때 제공 하는 다양 한 조합 및 선택 사항을 자세히 설명 합니다. 데이터 집합 매핑을 구성 하는 방법에 대 한 자세한 내용은 [데이터 집합 매핑을 구성 하는 방법](how-to-configure-mapping.md)을 참조 하세요.

|  | Azure Blob Storage | Azure SQL Data Lake Gen1 | Azure SQL Data Lake Gen2 | Azure SQL Database | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ ||✓|
| Azure Data Lake Storage Gen1 |✓ | |✓|
| Azure Data Lake Storage Gen2 |✓ | |✓|
| Azure SQL Database |✓ | |✓|✓|✓|
| Azure Synapse Analytics |✓ | |✓|✓|✓|

## <a name="next-steps"></a>다음 단계

데이터 공유를 시작하는 방법을 알아보려면 [데이터 공유](share-your-data.md) 자습서로 계속 진행하세요.
