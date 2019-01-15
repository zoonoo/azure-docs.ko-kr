---
title: Azure Stack의 프로필에서 지 원하는 리소스 공급자 API 버전 | Microsoft Docs
description: Azure Stack에서 프로필을 지 원하는 Azure Resource Manager 버전에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 2769b78632e1a7f776359f2a4d768154c224aab5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264617"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Azure Stack의 프로필에서 지 원하는 리소스 공급자 API 버전

이 문서의 Azure Stack에서 사용 하는 각 API 프로필에 대 한 리소스 공급자 및 버전 번호를 찾을 수 있습니다. 이 문서의 표에 각 리소스 공급자 및 API 버전의 프로필에 대 한 지원 되는 버전을 나열 합니다. 각 리소스 공급자에는 리소스 유형 및 특정 버전 번호의 집합을 포함 합니다.

API 프로필 3 명명 규칙을 사용합니다.

 - **latest**
 - **yyyy-mm-dd-hybrid**
 - **yyyy-mm-dd-profile**

Azure Stack에 대 한 API 프로필 버전 릴리스 작업 리듬에 한 설명과 참조 [Azure Stack에서 관리 하는 API 버전 프로필](azure-stack-version-profiles.md)합니다.

> [!Note]  
> 합니다 **최신** API 프로필 리소스 공급자 API의 최신 버전을 포함 하 고이 문서에 나열 되지 합니다.

## <a name="overview-of-2018--03-01-hybrid"></a>2018-03-01-하이브리드 개요

| 리소스 공급자 | Api 버전 |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-03-30 |
| Microsoft.Network | 2017-10-01<br>VPN Gateway 2017-03-01 됩니다. |
| Microsoft.Storage (데이터 평면) | 2017-04-17 |
| Microsoft.Storage (제어 평면) | 2016-01-01 |
| Microsoft. 웹 | 2016-08-01<br>Azure의 최신 (현재)은 무엇입니까 |
| Microsoft.KeyVault | 2016-10-01 (변경 되지 않음) |
| Microsoft.Resources (Azure Resource Manager 자체) | 2016-02-01 |
| Microsoft.Authorization (정책 작업) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| 정책 | 2016-10-01 |
| 리소스 | 2016-10-01 |
| Resources_Links | 2016-10-01 |
| Resources_Locks | 2016-10-01 |
| 구독 | 2016-10-01 |

더 API 프로필에서 공급자에 대 한 각 리소스 종류에 대 한 버전의 목록은 참조 하세요 [2018-03-01-하이브리드에 대 한 세부 정보](#details-for-the-2018-03-01-hybrid) 프로필입니다.

## <a name="overview-of-2018-03-01-hybrid"></a>2018-03-01-하이브리드 개요

| 리소스 공급자 | Api 버전 |
|------------------------------------------------|------------------------------|
| Microsoft.Compute | 2016-03-30 |
| Microsoft.Network | 2015-06-15 |
| Microsoft.Storage (데이터 평면) | 2015-04-05  |
| Microsoft.Storage (제어 평면) | 2016-01-01   |
| Microsoft.Websites | 2016-01-01 |
| Microsoft.KeyVault | 2016-10-01<br>(변경 되지 않음) |
| Microsoft.Resources<br>(Azure Resource Manager 자체) | 2016-02-01 |
| Microsoft.Authorization<Br>(정책 작업) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| 정책 | 2015-10-01-미리 보기 |
| 리소스 | 2016-02-01 |
| Resources_Links | 2016-09-01 |
| Resources_Locks | 2016-09-01 |
| 구독 | 2016-06-1 |

API 프로필에서 공급자에 대 한 각 리소스 종류의 버전 목록을 다음 섹션을 참조 하세요.

## <a name="details-for-the-2018-03-01-hybrid"></a>2018-03-01-하이브리드에 대 한 세부 정보

### <a name="microsoftauthorization"></a>Microsoft.Authorization

역할 기반 액세스 제어를 사용 하 여 작업을 관리 합니다 조직의 사용자가 리소스에서 수행할 수 있습니다. 이 작업 집합을 사용 하면 역할을 정의 사용자 또는 그룹에 역할 할당 및 사용 권한에 대 한 정보를 얻을 수 있습니다. 자세한 내용은 [권한 부여](/rest/api/authorization/)합니다.

| 리소스 종류 | API 버전 |
|---------------------|--------------------|
| 잠금 | 2017-04-01 |
| 작업 | 2015-07-01 |
| 권한 | 2015-07-01 |
| 정책 할당 | 2016-12-01 (2017-06-01-미리 보기) |
| 정책 정의 | 2016-12-01 |
| 공급자 작업 | 2015-07-01-preview |
| 역할 할당 | 2015-07-01 |
| 역할 정의 | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| 리소스 종류 | API 버전 |
|----------------------------------|----------------------|
| 위임 된 공급자 구독 | 2015-06-01-미리 보기 |
| 위임 된 사용 현황 집계 | 2015-06-01-미리 보기 |
| 예상 리소스 비용 | 2015-06-01-미리 보기 |
| 작업 | 2015-06-01-미리 보기 |
| 구독자 사용 현황 집계 | 2015-06-01-미리 보기 |
| 사용 현황 집계 | 2015-06-01-미리 보기 |

### <a name="microsoftcompute"></a>Microsoft.Compute

Azure Compute Api 가상 컴퓨터 및 해당 지원 리소스에 프로그래밍 방식으로 액세스할 수 있습니다. 자세한 내용은 [Azure Compute](/rest/api/compute/)합니다.

| 리소스 종류 | API 버전 |
|---------------------------------------------------------------|-------------|
| 가용성 집합 | 2016-03-30 |
| 위치 | 2016-03-30 |
| 위치/작업 | 2016-03-30 |
| Locations/publishers | 2016-03-30 |
| 위치/사용 | 2016-03-30 |
| Locations/vmSizes | 2016-03-30 |
| 작업 | 2016-03-30 |
| Virtual Machines | 2016-03-30 |
| Virtual Machines/확장 | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| 가상 머신 확장 집합/확장 | 2016-03-30 |
| 가상 머신 확장 집합/네트워크 인터페이스 | 2016-03-30 |
| 가상 머신 확장 집합/가상 머신 | 2016-03-30 |
| 가상 머신 크기 집합/virtualMachines/networkInterfaces | 2016-03-30 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| 리소스 종류 | API 버전 |
|------------------|-------------|
| 큐레이션 | 2015-04-01 |
| 큐레이션 콘텐츠 | 2015-04-01 |
| 큐레이션 추출 | 2015-04-01 |
| 갤러리 항목 | 2015-04-01 |
| 작업 | 2015-04-01 |
| 포털 | 2015-04-01 |
| 검색 | 2015-04-01 |
| 추천 | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| 리소스 종류 | API 버전 |
|--------------------|--------------------|
| 작업 | 2015-04-01 |
| 이벤트 유형 | 2015-04-01 |
| 이벤트 범주 | 2015-04-01 |
| 메트릭 정의 | 2018-01-01 |
| 메트릭 | 2018-01-01 |
| 진단 설정 | 2017-05-01-미리 보기 |
| 진단 설정 범주 | 2017-05-01-미리 보기 |


### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

키, 암호 및 키 자격 증명 모음 내에서 인증서 및 자격 증명 모음 키를 관리 합니다. 자세한 내용은 참조는 [Azure Key Vault REST API 참조](/rest/api/keyvault/)합니다.

| 리소스 종류 | API 버전 |
|-------------------------|--------------|
| 작업 | 2016-10-01 |
| 자격 증명 모음 | 2016-10-01 |
| 자격 증명 모음 / 액세스 정책 | 2016-10-01 |
| Vaults/secrets | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

작업 호출 결과는 사용 가능한 네트워크 클라우드 작업 목록 표현입니다. 자세한 내용은 [REST API 작업](/rest/api/operation/)합니다.

| 리소스 종류 | API 버전 |
|---------------------------|--------------|
| 연결 | 2015-06-15 |
| DNS 영역 | 2016-04-01 |
| 부하 분산 장치 | 2015-06-15 |
| 로컬 네트워크 게이트웨이 | 2015-06-15 |
| 위치 | 2016-04-01 |
| Location/operationResults | 2016-04-01 |
| 위치/작업 | 2016-04-01 |
| 위치/사용 | 2016-04-01 |
| 네트워크 인터페이스 | 2015-06-15 |
| 네트워크 보안 그룹 | 2015-06-15 |
| 작업 | 2015-06-15 |
| 공용 IP 주소 | 2015-06-15 |
| 경로 테이블 | 2015-06-15 |
| Virtual Network 게이트웨이 | 2015-06-15 |
| Virtual Network | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager를 사용하면 Azure 솔루션 인프라를 배포하고 관리할 수 있습니다. 리소스 그룹의 관련 리소스를 구성하고 JSON 템플릿을 사용하여 리소스를 배포합니다. 참조에 대 한 Resource Manager로 리소스 배포 및 관리를 소개 합니다 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)합니다.

| 리소스 종류 | API 버전 |
|-----------------------------------------|-------------------|
| 응용 프로그램 등록 | 2015-01-01 |
| 리소스 이름 확인 | 2016-09-01 |
| 위임 된 공급자 | 2015-01-01 |
| 위임 된 공급자/제안 | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| 배포 | 2016-09-01 |
| 배포/작업 | 2016-09-01 |
| 확장 메타 데이터 | 2015-01-01 |
| 링크 | 2016-09-01 |
| 위치 | 2015-01-01 |
| 제품 | 2015-01-01 |
| 작업 | 2015-01-01 |
| 공급자 | 2017-08-01 |
| 리소스 그룹 | 2016-09-01 |
| 리소스 | 2016-09-01 |
| 구독 | 2016-09-01 |
| 구독/위치 | 2016-09-01 |
| 구독/작업 결과 | 2016-09-01 |
| 구독/공급자 | 2017-08-01 |
| 구독/리소스 그룹 | 2016-09-01 |
| Subscriptions/resourceGroups/resources | 2016-09-01 |
| 구독/리소스 | 2016-09-01 |
| Subscriptions/tagNames | 2016-09-01 |
| Subscriptions/tagNames/tagValues | 2016-09-01 |
| 테 넌 트 | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

(SRP (저장소 리소스 공급자)를 사용 하면 저장소 계정 및 키를 프로그래밍 방식으로 관리할 수 있습니다. 자세한 내용은 참조는 [Azure Storage 리소스 공급자 REST API 참조](/rest/api/storagerp/)합니다.

| 리소스 종류 | API 버전 |
|-------------------------|--------------|
| 이름 가용성을 확인합니다. | 2016-01-01 |
| 위치 | 2016-01-01 |
| 위치/할당량 | 2016-01-01 |
| 작업 | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| 사용 | 2016-01-01 |

## <a name="details-for-the-2018-03-01-hybrid"></a>2018-03-01-하이브리드에 대 한 세부 정보

### <a name="microsoft-authorization"></a>Microsoft 인증

| 리소스 종류 | API 버전 |
|---------------------|---------------------------------|
| 잠금 | 2017-04-01 |
| 작업 | 2015-07-01 |
| 권한 | 2015-07-01 |
| 정책 할당 | 2016-12-01 (2017-06-01-미리 보기) |
| 정책 정의 | 2016-12-01 |
| 공급자 작업 | 2015-07-01-preview |
| 역할 할당 | 2015-07-01 |
| 역할 정의 | 2015-07-01 |

### <a name="microsoftcompute"></a>Microsoft.Compute

| 리소스 종류 | API 버전 |
|---------------------------------------------------------------|-------------|
| 가용성 집합 | 2016-03-30 |
| 위치 | 2016-03-30 |
| 위치/작업 | 2016-03-30 |
| Locations/publishers | 2016-03-30 |
| 위치/사용 | 2016-03-30 |
| Locations/vmSizes | 2016-03-30 |
| 작업 | 2016-03-30 |
| Virtual Machines | 2016-03-30 |
| Virtual Machines/확장 | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| 가상 머신 확장 집합/확장 | 2016-03-30 |
| 가상 머신 확장 집합/네트워크 인터페이스 | 2016-03-30 |
| 가상 머신 확장 집합/가상 머신 | 2016-03-30 |
| 가상 머신 크기 집합/virtualMachines/networkInterfaces | 2016-03-30 |

### <a name="microsoftnetwork"></a>Microsoft.Network

| 리소스 종류 | API 버전 |
|---------------------------|--------------|
| 연결 | 2015-06-15 |
| DNS 영역 | 2016-04-01 |
| 부하 분산 장치 | 2015-06-15 |
| 로컬 네트워크 게이트웨이 | 2015-06-15 |
| 위치 | 2016-04-01 |
| Location/operationResults | 2016-04-01 |
| 위치/작업 | 2016-04-01 |
| 위치/사용 | 2016-04-01 |
| 네트워크 인터페이스 | 2015-06-15 |
| 네트워크 보안 그룹 | 2015-06-15 |
| 작업 | 2015-06-15 |
| 공용 IP 주소 | 2015-06-15 |
| 경로 테이블 | 2015-06-15 |
| Virtual Network 게이트웨이 | 2015-06-15 |
| Virtual Network | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

| 리소스 종류 | API 버전 |
|-----------------------------------------|--------------|
| 응용 프로그램 등록 | 2015-01-01 |
| 리소스 이름 확인 | 2016-09-01 |
| 위임 된 공급자 | 2015-01-01 |
| 위임 된 공급자/제안 | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| 배포 | 2016-09-01 |
| 배포/작업 | 2016-09-01 |
| 확장 메타 데이터 | 2015-01-01 |
| 링크 | 2016-09-01 |
| 위치 | 2015-01-01 |
| 제품 | 2015-01-01 |
| 작업 | 2015-01-01 |
| 공급자 | 2017-08-01 |
| 리소스 그룹 | 2016-09-01 |
| 리소스 | 2016-09-01 |
| 구독 | 2016-09-01 |
| 구독/위치 | 2016-09-01 |
| 구독/작업 결과 | 2016-09-01 |
| 구독/공급자 | 2017-08-01 |
| 구독/리소스 그룹 | 2016-09-01 |
| Subscriptions/resourceGroups/resources | 2016-09-01 |
| 구독/리소스 | 2016-09-01 |
| Subscriptions/tagNames | 2016-09-01 |
| Subscriptions/tagNames/tagValues | 2016-09-01 |
| 테 넌 트 | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

| 리소스 종류 | API 버전 |
|-------------------------|--------------|
| 이름 가용성을 확인합니다. | 2016-01-01 |
| 위치 | 2016-01-01 |
| 위치/할당량 | 2016-01-01 |
| 작업 | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| 사용 | 2016-01-01 |

## <a name="next-steps"></a>다음 단계

* [Azure Stack용 PowerShell 설치](azure-stack-powershell-install.md)
* [Azure Stack 사용자의 PowerShell 환경 구성](azure-stack-powershell-configure-user.md)  
