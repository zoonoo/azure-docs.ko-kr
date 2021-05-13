---
title: Azure Purview 계정 연결 
description: Synapse 작업 영역에 Azure Purview 계정을 연결합니다.
author: Jejiang
ms.service: synapse-analytics
ms.subservice: purview
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: jejiang
ms.reviewer: jrasnick
ms.openlocfilehash: f0af3b571b1a6d793668c33d0c76e19a3d0c9e62
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109716063"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>빠른 시작: Synapse 작업 영역에 Azure Purview 계정 연결 


이 빠른 시작에서는 Azure Purview 계정을 Synapse 작업 영역에 등록합니다. 이렇게 연결하면 Synapse 기능을 통해 Azure Purview 자산을 찾아서 상호 작용할 수 있습니다. 

Synapse에서는 다음과 같은 작업을 수행할 수 있습니다. 
- 위쪽의 검색 상자에서 키워드를 사용하여 Purview 자산 찾기 
- 메타데이터, 계보, 주석을 기반으로 데이터 이해 
- 연결된 서비스 또는 통합 데이터 세트를 사용하여 해당 데이터를 작업 영역에 연결 
- Synapse Apache Spark, Synapse SQL 및 Data Flow를 사용하여 이러한 데이터 세트 분석 

## <a name="prerequisites"></a>사전 요구 사항 
- [Azure  Purview 계정](../../purview/create-catalog-portal.md) 
- [Synapse 작업 영역](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>Synapse 작업 영역에 로그인 

 [https://web.azuresynapse.net](https://web.azuresynapse.net)으로 이동하여 작업 영역에 로그인합니다. 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Azure Purview 계정에 연결할 수 있는 권한 

- Azure Purview 계정을 Synapse 작업 영역에 연결하려면 Azure Portal IAM의 Synapse 작업 영역에서 **기여자** 역할이 있어야 하고 해당 Azure Purview 계정에 대한 액세스 권한이 필요합니다. 자세한 내용은 [Azure Purview 권한](../../purview/catalog-permissions.md)을 참조하세요.

## <a name="connect-an-azure-purview-account"></a>Azure Purview 계정 연결  

- Synapse 작업 영역에서 **관리** -> **Azure Purview** 로 이동합니다. **Purview 계정에 연결** 을 선택합니다. 
- **Azure 구독에서 선택** 또는 **수동으로 입력** 을 선택할 수 있습니다. **Azure 구독에서 선택** 을 선택할 경우 액세스 가능한 계정을 선택할 수 있습니다. 
- 연결되면 **Azure Purview 계정** 탭에 Purview 계정 이름이 표시됩니다. 
- Synapse 작업 영역의 위쪽 가운데에 있는 검색 창을 사용하여 데이터를 검색할 수 있습니다. 

## <a name="nextsteps"></a>다음 단계 

[Azure Purview에서 Azure Synapse 자산 등록 및 검사](../../purview/register-scan-azure-synapse-analytics.md)

[Azure Purview를 사용하여 Synapse에서 데이터 검색, 연결 및 탐색](how-to-discover-connect-analyze-azure-purview.md)   
