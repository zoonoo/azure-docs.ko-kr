---
title: Azure Stack Edge의 리소스 공급자 API 프로필 버전 | Microsoft Docs
description: Azure Stack Edge의 프로필에서 지원되는 Azure Resource Manager API 버전에 대해 알아봅니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2021
ms.author: alkohli
ms.openlocfilehash: 485b1f8dccf104a9d31abb9aa79fd1ea4eb3940a
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111965344"
---
# <a name="resource-provider-api-profile-versions-for-azure-stack-edge"></a>Azure Stack Edge의 리소스 공급자 API 프로필 버전


[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge에서 사용하는 각 API 프로필의 리소스 공급자와 버전 번호를 찾을 수 있습니다. 이 문서의 표에는 각 리소스 공급자와 API 프로필 버전에서 지원되는 버전이 나와 있습니다. 리소스 공급자마다 일련의 리소스 종류와 특정 버전 번호가 있습니다.

API 프로필은 다음과 같은 세 가지 명명 규칙을 사용합니다.

- **최신**
- **yyyy-mm-dd-hybrid**
- **yyyy-mm-dd-profile**


## <a name="overview-of-the-2019-03-01-hybrid-profile"></a>2019-03-01-hybrid 프로필 개요

|리소스 공급자                                   |API 버전 |
|----------------------------------------------------|------------|
|Microsoft.Compute                                   |2017-12-01  |
|Microsoft.Network                                   |2017-10-01<br>VPN Gateway는 2017-10-01입니다. |
|Microsoft.Storage(데이터 평면)                      |2019-07-07  |
|Microsoft.Storage(컨트롤 플레인)                   |2019-06-01  |
|Microsoft.Resources(Azure Resource Manager 자체) |2020-06-01  |
|Microsoft.Authorization(정책 작업)         |2016-09-01  |

API 프로필의 공급자에 대한 리소스 종류별 버전 목록은 [2019-03-01-hybrid 프로필 세부 정보](/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions?view=azs-2008&preserve-view=true#details-for-the-2019-03-01-hybrid-profile)를 참조하세요.

## <a name="details-for-the-2019-03-01-hybrid-profile"></a>2019-03-01-hybrid 프로필 세부 정보

### <a name="microsoftcompute"></a>Microsoft.Compute

Azure 컴퓨팅 API는 가상 머신 및 지원 리소스에 프로그래밍 방식으로 액세스할 수 있도록 합니다. 자세한 내용은 [Azure Compute](/rest/api/compute/)를 참조하세요.

|리소스 종류               |API 버전 |
|----------------------------|------------|
|위치                   |2017-12-01  |
|Locations/vmSizes           |2017-12-01  |
|Virtual Machines            |2017-12-01  |
|Virtual Machines/extensions |2017-12-01  |

### <a name="microsoftnetwork"></a>Microsoft.Network

작업 호출 결과는 사용 가능한 네트워크 클라우드 작업 목록 표시입니다. 자세한 내용은 [작업 REST API](/rest/api/operation/)를 참조하세요.

|리소스 종류     |API 버전|
|-------------------|------------|
|네트워크 인터페이스 |2017-10-01  |
|가상 네트워크   |2017-10-01  |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager를 사용하면 Azure 솔루션 인프라를 배포하고 관리할 수 있습니다. 리소스 그룹의 관련 리소스를 구성하고 JSON 템플릿을 사용하여 리소스를 배포합니다. Resource Manager를 사용하여 리소스를 배포하고 관리하는 방법에 대한 소개는 [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md)를 참조하세요.

|리소스 종류                        |API 버전|
|--------------------------------------|------------|
|배포                           |2020-06-01  |
|Deployments/operations                |2020-06-01  |
|링크                                 |2020-06-01  |
|위치                             |2020-06-01  |
|작업                            |2020-06-01  |
|공급자                             |2020-06-01  |
|ResourceGroups                        |2020-06-01  |
|리소스                             |2020-06-01  |
|Subscriptions                         |2018-09-01  |
|구독/위치               |2018-09-01  |
|Subscriptions/operationresults        |2020-06-01  |
|subscriptions / providers               |2020-06-01  |
|Subscriptions/ResourceGroups          |2020-06-01  |
|Subscriptions/resourceGroups/resources|2020-06-01  |
|Subscriptions/resources               |2020-06-01  |
|Subscriptions/tagNames                |2020-06-01  |
|Subscriptions/tagNames/tagValues      |2020-06-01  |
|테넌트                               |2018-09-01  |

### <a name="microsoftstorage"></a>Microsoft.Storage

SRP(저장소 리소스 공급자)를 사용하면 스토리지 계정과 키를 프로그래밍 방식으로 관리할 수 있습니다. 자세한 내용은 [Azure Storage 리소스 공급자 REST API 참조](/rest/api/storagerp/)를 확인하세요.

|리소스 종류       |API 버전|
|---------------------|------------|
|CheckNameAvailability|2019-06-01  |
|위치            |2019-06-01  |
|Locations/quotas     |2019-06-01  |
|작업           |2019-06-01  |
|StorageAccounts      |2019-06-01  |
|사용               |2019-06-01  |

## <a name="next-steps"></a>다음 단계

- [Windows PowerShell을 통해 Azure Stack Edge Pro GPU 디바이스 관리](azure-stack-edge-gpu-connect-powershell-interface.md)