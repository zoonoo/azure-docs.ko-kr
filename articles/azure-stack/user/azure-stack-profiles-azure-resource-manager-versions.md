---
title: Azure 스택에서 프로필에서 지 원하는 리소스 공급자 API 버전 | Microsoft Docs
description: Azure 스택에서 프로필에서 지 원하는 Azure 리소스 관리자 버전에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: db01df21c95ee41197344cec719f1c2ab2dfc2ed
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Azure 스택에서 프로필에서 지 원하는 리소스 공급자 API 버전

Azure 리소스 공급자 리소스를 배포 하 고 Azure 리소스 관리자를 통해 관리할 수 있습니다를 제공 합니다. 각 공급자 리소스를 사용 하기 위한 작업을 제공 합니다. 몇 가지 일반 리소스 공급자 Microsoft.Compute이 가상 컴퓨터를 제공, 등 Microsoft.Storage이 저장소 계정 리소스를 제공 Microsoft.Web이 웹 앱에 관련 된 리소스를 제공 합니다. 자세한 내용은 [리소스 공급자 및 형식](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services)을 참조하세요.

각 리소스 공급자에 대해 다음 표에서 프로필을 사용할 때 Azure 스택에 대 한 API 버전에서 지원 되는 버전을 나타냅니다.

### <a name="microsoftauthorization"></a>Microsoft.Authorization

역할 기반 액세스 제어를 사용 하 여 조직의 사용자가 리소스에서 수행할 수 있는 작업을 관리 합니다. 이 집합의 작업을 사용 하면 역할 정의 사용자 또는 그룹에 역할 할당 및 사용 권한에 대 한 정보를 얻을 수 있습니다. 자세한 내용은 참조 [권한 부여](https://docs.microsoft.com/rest/api/authorization/)합니다.

| 리소스 종류 | API 버전 |
|---------------------|--------------------|
| 잠금 | 2017-04-01 |
| 작업 | 2015-07-01 |
| 권한 | 2015-07-01 |
| 정책 할당 | 2016-12-01 (2017-06-01-미리 보기) |
| 정책 정의 | 2016-12-01 |
| 공급자 작업 | 2015-07-01-미리 보기 |
| 역할 할당 | 2015-07-01 |
| 역할 정의 | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| 리소스 종류 | API 버전 |
|----------------------------------|----------------------|
| 위임 된 공급자 구독 | 2015-06-01-미리 보기 |
| 위임 된 사용 현황 집계 | 2015-06-01-미리 보기 |
| 예상 리소스 소비 | 2015-06-01-미리 보기 |
| 작업 | 2015-06-01-미리 보기 |
| 구독자 사용량 집계 | 2015-06-01-미리 보기 |
| 사용 현황 집계 | 2015-06-01-미리 보기 |

### <a name="microsoftcompute"></a>Microsoft.Compute

Azure 계산 Api 가상 컴퓨터 및 해당 관련 리소스에 프로그래밍 방식으로 액세스할 수 있습니다. 자세한 내용은 참조 [Azure 계산](https://docs.microsoft.com/rest/api/compute/)합니다.

| 리소스 종류 | API 버전 |
|---------------------------------------------------------------|-------------|
| 가용성 집합 | 2016-03-30 |
| 위치 | 2016-03-30 |
| 위치/작업 | 2016-03-30 |
| Locations/publishers | 2016-03-30 |
| 위치/용도 | 2016-03-30 |
| 위치/vmSizes | 2016-03-30 |
| 작업 | 2016-03-30 |
| Virtual Machines | 2016-03-30 |
| 가상 컴퓨터/확장 | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| 가상 컴퓨터 크기 집합/확장 | 2016-03-30 |
| 가상 컴퓨터 크기 집합 네트워크 인터페이스 | 2016-03-30 |
| 가상 컴퓨터 크기 조정 설정/가상 컴퓨터 | 2016-03-30 |
| 가상 컴퓨터 크기 조정 설정/virtualMachines/networkInterfaces | 2016-03-30 |

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
| 경고 규칙 | 2016-03-01 |
| 이벤트 범주 | 2017-03-01-미리 보기 |
| 이벤트 유형 | 2017-03-01-미리 보기 |
| 메트릭 정의 | 2016-03-01 |
| 작업 | 2015-04-01 |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

키, 비밀 정보 및 사용자 키 자격 증명 모음 내에서 인증서 자격 증명 모음 키를 관리 합니다. 자세한 내용은 참조 [Azure 키 자격 증명 모음 REST API 참조](https://docs.microsoft.com/rest/api/keyvault/)합니다.

| 리소스 종류 | API 버전 |
|-------------------------|--------------|
| 작업 | 2016-10-01 |
| 자격 증명 모음 | 2016-10-01 |
| 자격 증명 모음 / 액세스 정책 | 2016-10-01 |
| Vaults/secrets | 2016-10-01 |

### <a name="microsoftkeyvaultadmin"></a>Microsoft.Keyvault.Admin

키, 비밀 정보 및 사용자 키 자격 증명 모음 내에서 인증서 자격 증명 모음 키를 관리 합니다. 자세한 내용은 참조 [Azure 키 자격 증명 모음 REST API 참조](https://docs.microsoft.com/rest/api/keyvault/)합니다.

| 리소스 종류 | API 버전 |
|------------------|--------------------|
| 위치 | 2017-02-01-미리 보기 |
| 위치/할당량 | 2017-02-01-미리 보기 |

### <a name="microsoftnetwork"></a>Microsoft.Network

작업 호출 결과는 사용 가능한 네트워크 클라우드 작업 목록의 표현입니다. 자세한 내용은 참조 [REST API 작업](https://docs.microsoft.com/rest/api/operation/)합니다.

| 리소스 종류 | API 버전 |
|---------------------------|--------------|
| 연결 | 2015-06-15 |
| DNS 영역 | 2016-04-01 |
| 부하 분산 장치 | 2015-06-15 |
| 로컬 네트워크 게이트웨이 | 2015-06-15 |
| 위치 | 2016-04-01 |
| Location/operationResults | 2016-04-01 |
| 위치/작업 | 2016-04-01 |
| 위치/용도 | 2016-04-01 |
| 네트워크 인터페이스 | 2015-06-15 |
| 네트워크 보안 그룹 | 2015-06-15 |
| 작업 | 2015-06-15 |
| 공용 IP 주소 | 2015-06-15 |
| 경로 테이블 | 2015-06-15 |
| Virtual Network 게이트웨이 | 2015-06-15 |
| Virtual Network | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager를 사용하면 Azure 솔루션 인프라를 배포하고 관리할 수 있습니다. 리소스 그룹의 관련 리소스를 구성하고 JSON 템플릿을 사용하여 리소스를 배포합니다. 리소스 관리자와 리소스 배포 및 관리 하는 방법을 알려면 [Azure 리소스 관리자 개요](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)합니다.

| 리소스 종류 | API 버전 |
|-----------------------------------------|-------------------|
| 응용 프로그램 등록 | 2015-01-01 |
| 리소스 이름 확인 | 2015-012016-09-01 |
| 위임 된 공급자 | 2015-01-01 |
| 위임 된 공급자/제안 | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| 배포 | 2016-0209-01 |
| 배포/작업 | 2016-0209-01 |
| 확장 메타 데이터 | 2015-01-01 |
| 링크 | 2015-012016-09-01 |
| 위치 | 2015-01-01 |
| 제품 | 2015-01-01 |
| 작업 | 2015-01-01 |
| 공급자 | 2015-012017-08-01 |
| 리소스 그룹 | 2015-012016-09-01 |
| 리소스 | 2015-012016-09-01 |
| 구독 | 2015-012016-09-01 |
| 구독/위치 | 2015-012016-09-01 |
| 구독/작업 결과 | 2015-012016-09-01 |
| 구독/공급자 | 2015-012017-08-01 |
| 구독/리소스 그룹 | 2015-012016-09-01 |
| Subscriptions/resourceGroups/resources | 2015-012016-09-01 |
| 구독/리소스 | 2015-012016-09-01 |
| Subscriptions/tagNames | 2016-0609-01 |
| 구독/tagNames/tagValues | 2016-0609-01 |
| 테 넌 트 | 2015-012017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

저장소 리소스 공급자 (SRP)를 사용 하면 저장소 계정 및 키를 프로그래밍 방식으로 관리할 수 있습니다. 자세한 내용은 참조 [Azure 저장소 리소스 공급자 REST API 참조](https://docs.microsoft.com/rest/api/storagerp/)합니다.

| 리소스 종류 | API 버전 |
|-------------------------|--------------|
| 이름 가용성을 확인합니다. | 2016-01-01 |
| 위치 | 2016-01-01 |
| 위치/할당량 | 2016-01-01 |
| 작업 | 2016-01-01 |
| storageaccounts 하는 중 | 2016-01-01 |
| 사용 | 2016-01-01 |

## <a name="next-steps"></a>다음 단계

* [Azure Stack용 PowerShell 설치](azure-stack-powershell-install.md)
* [Azure 스택 사용자의 PowerShell 환경 구성](azure-stack-powershell-configure-user.md)  
