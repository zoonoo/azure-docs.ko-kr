---
title: Azure Cloud Services(클래식)를 Azure Cloud Services(확장 지원)로 마이그레이션
description: Cloud Services(클래식)에서 Cloud Service(확장 지원)로의 마이그레이션 개요
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: f3b41bed481ff36ec1f2fef8e699e57f6138474c
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108285937"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>Azure Cloud Services(클래식)를 Azure Cloud Services(확장 지원)로 마이그레이션

이 문서에서는 플랫폼 지원 마이그레이션 도구 및 이를 사용하여 [Azure Cloud Services(클래식)](../cloud-services/cloud-services-choose-me.md)를 [Azure Cloud Services(확장 지원)](overview.md)로 마이그레이션하는 방법에 대해 간략하게 설명합니다.

마이그레이션 도구는 동일한 API를 활용하고, [가상 머신(클래식) 마이그레이션](../virtual-machines/migration-classic-resource-manager-overview.md)과 동일한 환경을 제공합니다. 

> [!IMPORTANT]
> 마이그레이션 도구를 사용하여 Cloud Services(클래식)에서 Cloud Services(확장 지원)로 마이그레이션하는 것은 현재 공개 미리 보기에 있습니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

마이그레이션에 대한 도움이 필요한 경우 다음 리소스를 참조하세요. 

- [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html): 마이그레이션에 대한 Microsoft 및 커뮤니티 지원
- [Azure 마이그레이션 지원](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%22pesId%22:%22e79dcabe-5f77-3326-2112-74487e1e5f78%22,%22supportTopicId%22:%22fca528d2-48bd-7c9f-5806-ce5d5b1d226f%22%7D): 마이그레이션 기간의 기술 지원을 위한 전용 지원팀. 기술 지원이 없는 고객은 이 마이그레이션을 위해 특별히 제공되는 [무료 지원 기능](https://aka.ms/cs-migration-errors)을 사용할 수 있습니다.
- 회사/조직에서 Microsoft와 파트너 관계를 맺고 있거나 클라우드 솔루션 설계자 또는 기술 계정 관리자와 같은 Microsoft 담당자와 협력하는 경우 마이그레이션에 필요한 추가 리소스를 요청합니다.
- Cloud Services(확장 지원) 제품 팀에 피드백을 제공하거나 문제를 제기하려면 [이 설문 조사](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR--AgudUMwJKgRGMO84rHQtUQzZYNklWUk4xOTFXVFBPOFdGOE85RUIwVC4u)를 완료합니다. 

## <a name="migration-benefits"></a>마이그레이션 이점
플랫폼 지원 마이그레이션의 주요 이점은 다음과 같습니다.
- 마이그레이션은 플랫폼에서 완전하게 오케스트레이션되어 전체 배포 및 관련 리소스를 Azure Resource Manager로 이동합니다.
- 가동 중지 시간 마이그레이션이 없습니다.
- 수동 작업을 최소화하여 다른 마이그레이션 경로에 비해 쉽고 빠르게 마이그레이션할 수 있습니다. 
- 마이그레이션의 일부로 Cloud Services IP 주소 및 DNS 레이블을 유지합니다. 

다른 이점 및 마이그레이션해야 하는 이유는 [Cloud Services(확장 지원)](overview.md) 및 [Azure Resource Manager](../azure-resource-manager/management/overview.md)를 참조하세요. 

## <a name="setup-access-for-migration"></a>마이그레이션을 위한 액세스 설정

이 마이그레이션을 수행하려면 사용자는 구독에 대한 공동 관리자로 추가되고, 필요한 공급자를 등록해야 합니다. 

1. Azure Portal에 로그인합니다.
3. 허브 메뉴에서 구독을 선택합니다. 표시되지 않으면 모든 서비스를 선택합니다.
3. 적절한 구독 항목을 찾은 다음, [내 역할] 필드를 확인합니다. 공동 관리자의 경우 값은 계정 관리자여야 합니다. 공동 관리자를 추가할 수 없는 경우 구독에 대한 서비스 관리자 또는 공동 관리자에게 문의하여 사용자 자신을 추가합니다.

4. [Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) 또는 [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)를 사용하여 Microsoft.ClassicInfrastructureMigrate 네임스페이스에 대한 구독을 등록합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. [Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) 또는 [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)를 사용하여 Cloud Services 마이그레이션 미리 보기 기능에 대한 구독을 등록합니다.

    ```powershell
    Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

6. 등록 상태를 확인합니다. 등록을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 

    ```powershell
    Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

## <a name="how-is-migration-for-cloud-services-classic-different-from-virtual-machines-classic"></a>Cloud Services(클래식) 마이그레이션은 Virtual Machines(클래식)와 어떻게 다른가요?
Azure Service Manager는 [Azure Virtual Machines(클래식)](/previous-versions/azure/virtual-machines/windows/classic/tutorial-classic) 및 [Azure Cloud Services(클래식)](../cloud-services/cloud-services-choose-me.md) 또는 웹/작업자 역할의 두 가지 컴퓨팅 제품을 지원합니다. 두 제품은 Cloud Service 내의 배포 유형에 따라 달라집니다. Azure Cloud Services(클래식)는 웹/작업자 역할을 사용하는 배포가 포함된 Cloud Service를 사용합니다. Azure Virtual Machines(클래식)는 IaaS VM을 사용하는 배포가 포함된 클라우드 서비스를 사용합니다.

지원되는 시나리오 목록은 배포 유형의 차이로 인해 Cloud Services(클래식)와 Virtual Machines(클래식) 간에 다릅니다.

## <a name="migration-steps"></a>마이그레이션 단계
 
고객은 Virtual Machines(클래식)를 마이그레이션하는 데 사용되는 것과 동일한 4가지 작업을 사용하여 Cloud Services(클래식) 배포를 마이그레이션할 수 있습니다. 

1. **마이그레이션 유효성 검사** - 지원되지 않는 일반적인 시나리오에서 마이그레이션을 차단하지 않는지 확인합니다.
2. **마이그레이션 준비** – Azure Resource Manager에서 리소스 메타데이터를 복제합니다. 리소스 메타데이터가 Azure 서버 관리자와 Azure Resource Manager 간에 동기화되도록 만들기/업데이트/삭제 작업에 대한 모든 리소스가 잠깁니다. 모든 읽기 작업은 Cloud Services(클래식) 및 Cloud Services(확장 지원) API를 모두 사용하여 작동합니다.
3. **마이그레이션 중단** - Azure Resource Manager에서 리소스 메타데이터를 제거합니다. 만들기/업데이트/삭제 작업에 대한 모든 리소스의 잠금을 해제합니다.
4. **마이그레이션 커밋** - Azure Service Manager에서 리소스 메타데이터를 제거합니다. 만들기/업데이트/삭제 작업에 대한 리소스의 잠금을 해제합니다. 커밋을 시도한 후에는 중단이 더 이상 허용되지 않습니다.

>[!NOTE]
> 준비, 중단 및 커밋은 멱등적(idempotent)이므로 실패한 경우 다시 시도하여 문제를 해결해야 합니다.

:::image type="content" source="media/in-place-migration-1.png" alt-text="마이그레이션과 관련된 단계의 다이어그램을 보여 주는 이미지":::

자세한 내용은 [클래식에서 Azure Resource Manager로 IaaS 리소스 플랫폼 지원 마이그레이션 개요](../virtual-machines/migration-classic-resource-manager-overview.md)를 참조하세요.

## <a name="supported-resources-and-features-available-for-migration-associated-with-cloud-services-classic"></a>Cloud Services(클래식)와 관련된 마이그레이션에 사용할 수 있는 지원되는 리소스 및 기능
-   스토리지 계정
-   가상 네트워크
-   네트워크 보안 그룹
-   예약된 공용 IP 주소
-   엔드포인트 액세스 제어 목록
-   사용자 정의 경로
-   내부 부하 분산 장치
-   키 자격 증명 모음으로 인증서 마이그레이션
-   플러그 인 및 확장(XML 및 Json 기반)
-   시작/중지 시 작업
-   가속화된 네트워킹을 사용하는 배포
-   단일 또는 여러 역할을 사용하는 배포
-   기본 부하 분산 장치
-   입력, 인스턴스 입력, 내부 엔드포인트
-   동적 공용 IP 주소
-   DNS 이름 
-   네트워크 트래픽 규칙
-   Hypernet 가상 네트워크

## <a name="supported-configurations--migration-scenarios"></a>지원되는 구성/마이그레이션 시나리오
이러한 시나리오는 리소스, 기능 및 Cloud Services의 조합과 관련된 상위 수준 시나리오입니다. 이 목록은 전체 목록이 아닙니다.

| 서비스 | 구성 | 의견 | 
|---|---|---|
| [Azure AD Domain Services](../active-directory-domain-services/migrate-from-classic-vnet.md) | Azure Active Directory Domain Services가 포함된 가상 네트워크 | Cloud Service 배포 및 Azure AD Domain Services가 모두 포함된 가상 네트워크를 지원합니다. 고객은 먼저 Azure AD Domain Services를 별도로 마이그레이션한 다음, Cloud Service 배포만 남아 있는 가상 네트워크를 마이그레이션해야 합니다. |
| 클라우드 서비스 | 단일 슬롯에만 배포가 포함된 Cloud Service | 프로덕션 또는 스테이징 슬롯 배포가 포함된 Cloud Services를 마이그레이션할 수 있습니다. |
| 클라우드 서비스 | 공개적으로 표시되는 가상 네트워크에 없는 배포(기본 가상 네트워크 배포) | Cloud Service는 공개적으로 표시되는 가상 네트워크, 숨겨진 가상 네트워크에 있을 수 있거나 가상 네트워크에 없을 수 있습니다.  숨겨진 가상 네트워크 및 공개적으로 표시되는 가상 네트워크의 Cloud Services는 마이그레이션에 지원됩니다. 고객은 유효성 검사 API를 사용하여 배포가 기본 가상 네트워크 내에 있는지 여부를 파악할 수 있습니다. 그러면 마이그레이션할 수 있는지 여부를 결정할 수 있습니다. |
|클라우드 서비스 | XML 확장(BGInfo, Visual Studio 디버거, 웹 배포 및 원격 디버깅) | 모든 xml 확장이 마이그레이션에 지원됩니다. 
| Virtual Network | 여러 Cloud Services가 포함된 가상 네트워크 가상 네트워크 | 여러 클라우드 서비스가 포함된 가상 네트워크가 마이그레이션에 지원됩니다. 가상 네트워크와 해당 네트워크 내의 모든 Cloud Services가 모두 Azure Resource Manager로 마이그레이션됩니다. |
| Virtual Network | Portal을 통해 만든 가상 네트워크의 마이그레이션(.cscfg 파일에서 "Group Resource-group-name VNet-Name"을 사용해야 함)  | 마이그레이션의 일부로 cscfg의 가상 네트워크 이름이 가상 네트워크의 Azure Resource Manager ID를 사용하도록 변경됩니다. (subscription/subscription-id/resource-group/resource-group-name/resource/vnet-name) <br><br>마이그레이션 후 배포를 관리하려면 가상 네트워크 이름 대신 Azure Resource Manager ID 사용을 시작하도록 .cscfg 파일의 로컬 복사본을 업데이트합니다. <br><br>이전 명명 체계를 사용하는 .cscfg 파일은 유효성 검사를 통과하지 못합니다. 
| Virtual Network | 다른 서브넷의 역할을 사용하는 배포 마이그레이션 | 다른 서브넷의 다른 역할을 사용하는 클라우드 서비스는 마이그레이션에 지원됩니다. |
    

## <a name="resources-and-features-not-available-for-migration"></a>마이그레이션에 사용할 수 없는 리소스 및 기능
이러한 시나리오는 리소스, 기능 및 Cloud Services의 조합과 관련된 상위 수준 시나리오입니다. 이 목록은 전체 목록이 아닙니다. 

| 리소스 | 다음 단계/해결 방법 | 
|---|---|
| 자동 크기 조정 규칙 | 마이그레이션은 진행되지만 규칙이 삭제됩니다. Cloud Services(확장 지원)에 대한 마이그레이션 후 [규칙을 다시 만듭니다](./configure-scaling.md). | 
| 경고 | 마이그레이션은 진행되지만 경고가 삭제됩니다. Cloud Services(확장 지원)에 대한 마이그레이션 후 [규칙을 다시 만듭니다](./enable-alerts.md). | 
| VPN Gateway | 마이그레이션을 시작하기 전에 VPN Gateway를 제거한 후 마이그레이션이 완료되면 VPN Gateway를 다시 만듭니다. | 
| ExpressRoute 게이트웨이(Virtual Network와 동일한 구독에서만) | 마이그레이션을 시작하기 전에 게이트웨이를 제거한 다음, 마이그레이션이 완료되면 ExpressRoute 게이트웨이를 다시 만듭니다. | 
| 할당량  | 할당량은 마이그레이션되지 않습니다. 유효성 검사가 성공하려면 마이그레이션하기 전에 Azure Resource Manager에서 [새 할당량을 요청](../azure-resource-manager/templates/error-resource-quota.md#solution)합니다. | 
| 선호도 그룹 | 지원 안 됨 마이그레이션하기 전에 선호도 그룹을 제거합니다.  | 
| [가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)을 사용하는 가상 네트워크| 피어링된 가상 네트워크를 다른 가상 네트워크로 마이그레이션하기 전에 피어링을 삭제하고, 가상 네트워크를 Resource Manager로 마이그레이션하고, 피어링을 다시 만듭니다. 이로 인해 아키텍처에 따라 가동 중지 시간이 발생할 수 있습니다. | 
| App Service 환경이 포함된 가상 네트워크 | 지원되지 않음 | 
| HDInsight Services가 포함된 가상 네트워크 | 지원 안 됨 
| Azure API Management 배포가 포함된 가상 네트워크 | 지원 안 됨 <br><br> 가상 네트워크를 마이그레이션하려면 API Management 배포의 가상 네트워크를 변경합니다. 가동 중지 시간이 없는 작업입니다. | 
| 클래식 ExpressRoute 회로 | 지원 안 됨 <br><br>이러한 회로는 PaaS 마이그레이션을 시작하기 전에 Azure Resource Manager로 마이그레이션해야 합니다. 자세한 내용은 [클래식에서 Resource Manager 배포 모델로 ExpressRoute 회로 이동](../expressroute/expressroute-howto-move-arm.md)을 참조하세요. |  
| 역할 기반 Access Control | 마이그레이션 후 리소스의 URI가 `Microsoft.ClassicCompute`에서 `Microsoft.Compute`로 변경됩니다. 마이그레이션 후 RBAC 정책을 업데이트해야 합니다. | 
| Application Gateway | 지원되지 않습니다. <br><br> 마이그레이션을 시작하기 전에 Application Gateway를 제거한 다음, Azure Resource Manager로의 마이그레이션이 완료되면 Application Gateway를 다시 만듭니다. | 

## <a name="unsupported-configurations--migration-scenarios"></a>지원되지 않는 구성/마이그레이션 시나리오

| 구성/시나리오  | 다음 단계/해결 방법 | 
|---|---|
| 가상 네트워크에 없는 일부 이전 배포 마이그레이션 | 가상 네트워크에 없는 일부 클라우드 서비스 배포는 마이그레이션에 지원되지 않습니다. <br><br> 1. 유효성 검사 API를 사용하여 배포를 마이그레이션할 수 있는지 확인합니다. <br> 2. 자격이 있는 경우 배포가 "DefaultRdfeVnet" 접두사가 있는 가상 네트워크 아래의 Azure Resource Manager로 이동됩니다. | 
| 동적 IP 주소를 사용하여 프로덕션 및 스테이징 슬롯 배포가 모두 포함된 배포 마이그레이션 | 2슬롯 Cloud Service를 마이그레이션하려면 스테이징 슬롯을 삭제해야 합니다. 스테이징 슬롯이 삭제되면 Azure Resource Manager에서 프로덕션 슬롯을 독립 Cloud Service(확장 지원)로 마이그레이션합니다. 그런 다음, 스테이징 환경을 새 Cloud Service(확장 지원)로 다시 배포하고, 첫 번째 서비스와 스왑할 수 있게 합니다. | 
| 예약된 IP 주소를 사용하여 프로덕션 및 스테이징 슬롯 배포가 모두 포함된 배포 마이그레이션 | 지원 안 됨 | 
| 다른 가상 네트워크에서 프로덕션 및 스테이징 배포 마이그레이션|2슬롯 Cloud Service를 마이그레이션하려면 스테이징 슬롯을 삭제해야 합니다. 스테이징 슬롯이 삭제되면 Azure Resource Manager에서 프로덕션 슬롯을 독립 Cloud Service(확장 지원)로 마이그레이션합니다. 그런 다음, 새 Cloud Services(확장 지원) 배포를 스왑 가능한 속성을 사용하도록 설정한 마이그레이션된 배포에 연결할 수 있습니다. 이전 스테이징 슬롯 배포의 배포 파일을 다시 사용하여 이 새 스왑 가능한 배포를 만들 수 있습니다. | 
| 빈 Cloud Service 마이그레이션(배포가 없는 Cloud Service) | 지원 안 됨 | 
| 원격 데스크톱 플러그 인 및 원격 데스크톱 확장이 포함된 배포 마이그레이션 | 옵션 1: 마이그레이션하기 전에 원격 데스크톱 플러그 인을 제거합니다. 이렇게 하려면 배포 파일을 변경해야 합니다. 그러면 마이그레이션이 진행됩니다. <br><br> 옵션 2: 원격 데스크톱 확장을 제거하고 배포를 마이그레이션합니다. 마이그레이션 후 플러그 인을 제거하고 확장을 설치합니다. 이렇게 하려면 배포 파일을 변경해야 합니다. <br><br> 마이그레이션하기 전에 플러그 인 및 확장을 제거합니다. 플러그 인은 Cloud Services(확장 지원)에서 [사용하지 않는 것이 좋습니다](./deploy-prerequisite.md#required-service-definition-file-csdef-updates).| 
| PaaS 및 IaaS 배포가 모두 포함된 가상 네트워크 |지원되지 않음 <br><br> PaaS 또는 IaaS 배포를 다른 가상 네트워크로 이동합니다. 이로 인해 가동 중지 시간이 발생합니다. | 
레거시 역할 크기(예: Small 또는 ExtraLarge)를 사용하는 Cloud Service 배포 | 마이그레이션이 완료되지만, 최신 역할 크기를 사용하도록 역할 크기가 업데이트됩니다. 비용 또는 SKU 속성은 변경되지 않으며, 이 변경으로 인해 가상 머신이 다시 부팅되지 않습니다. 이러한 최신 역할 크기를 참조하도록 모든 배포 아티팩트를 업데이트합니다. 자세한 내용은 [사용 가능한 VM 크기](available-sizes.md)를 참조하세요.|
| 다른 가상 네트워크로 Cloud Service 마이그레이션 | 지원되지 않음 <br><br> 1. 마이그레이션하기 전에 배포를 다른 클래식 가상 네트워크로 이동합니다. 이로 인해 가동 중지 시간이 발생합니다. <br> 2. 새 가상 네트워크를 Azure Resource Manager로 마이그레이션합니다. <br><br> 또는 <br><br> 1. 가상 네트워크를 Azure Resource Manager로 마이그레이션합니다. <br>2. Cloud Service를 새 가상 네트워크로 이동합니다. 이로 인해 가동 중지 시간이 발생합니다. | 
| 가상 네트워크의 Cloud Service이지만 명시적 서브넷이 할당되지 않음 | 지원 안 됨 완화에는 역할을 서브넷으로 이동하는 작업이 포함되며, 이 경우 역할을 다시 시작해야 합니다(가동 중지 시간). | 


## <a name="post-migration-changes"></a>마이그레이션 후 변경 내용
Cloud Services(클래식) 배포는 Cloud Service(확장 지원) 배포로 변환됩니다. 자세한 내용은 [Cloud Services(확장 지원) 설명서](deploy-prerequisite.md)를 참조하세요.  

### <a name="changes-to-deployment-files"></a>배포 파일 변경 내용 

배포 파일이 Azure Resource Manager 및 Cloud Services(확장 지원) 요구 사항을 준수하도록 고객의 .csdef 및 .cscfg 파일이 약간 변경됩니다. 마이그레이션 후 새 배포 파일을 검색하거나 기존 파일을 업데이트합니다. 이는 업데이트/삭제 작업을 수행하는 데 필요합니다.  

- Virtual Network는 .cscfg 파일의 NetworkConfiguration 섹션에 있는 리소스 이름 대신 전체 Azure Resource Manager 리소스 ID를 사용합니다. 예들 들어 `/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name`입니다. 클라우드 서비스와 동일한 리소스 그룹에 속한 가상 네트워크의 경우 가상 네트워크 이름만 사용하도록 .cscfg 파일을 다시 업데이트하도록 선택할 수 있습니다.  

- Small, Large, ExtraLarge와 같은 클래식 크기는 새 크기 이름(Standard_A*)으로 바뀝니다. 크기 이름은 .csdef 파일에서 새 이름으로 변경해야 합니다. 자세한 내용은 [Cloud Services(확장 지원) 배포 필수 구성 요소](deploy-prerequisite.md#required-service-definition-file-csdef-updates)를 참조하세요.

- 가져오기 API를 사용하여 배포 파일의 최신 복사본을 가져옵니다. 
    - [Portal](../azure-resource-manager/templates/export-template-portal.md), [PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates), [CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) 및 [Rest API](/rest/api/resources/resourcegroups/exporttemplate)를 사용하여 템플릿을 가져옵니다. 
    - [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) 또는 [Rest API](/rest/api/compute/cloudservices/rest-get-package)를 사용하여 .csdef 파일을 가져옵니다. 
    - [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) 또는 [Rest API](/rest/api/compute/cloudservices/rest-get-package)를 사용하여 .cscfg 파일을 가져옵니다. 
    
 

### <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>고객의 자동화, CI/CD 파이프라인, 사용자 지정 스크립트, 사용자 지정 대시보드, 사용자 지정 도구 등에 대한 변경 내용  

고객은 새 API/명령을 사용하여 배포를 관리하기 위해 도구 및 자동화를 업데이트해야 합니다. 고객은 이 변경의 일환으로 Azure Resource Manager/Cloud Services(확장 지원)의 새로운 특징과 기능을 쉽게 채택할 수 있습니다. 

- 마이그레이션 후 리소스 및 리소스 그룹 이름 변경
    - 마이그레이션의 일환으로 Cloud Service, 공용 IP 주소 등과 같은 몇 가지 리소스 이름이 변경됩니다. Cloud Service를 업데이트하기 전에 이러한 변경 내용을 배포 파일에 반영해야 할 수 있습니다. [변경되는 리소스 이름에 대해 자세히 알아보세요](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration).  

- 클라우드 서비스를 관리하고 크기를 조정하는 데 필요한 규칙 및 정책 다시 만들기 
    - [자동 크기 조정 규칙](configure-scaling.md)은 마이그레이션되지 않습니다. 마이그레이션 후 자동 크기 조정 규칙을 다시 만듭니다.  
    - [경고](enable-alerts.md)는 마이그레이션되지 않습니다. 마이그레이션 후 경고를 다시 만듭니다.
    - Key Vault는 액세스 정책 없이 만들어집니다. Key Vault에서 [적절한 정책을 만들어](../key-vault/general/assign-access-policy-portal.md) 인증서를 보거나 관리합니다. 인증서는 비밀 탭의 설정 아래에 표시됩니다.

## <a name="next-steps"></a>다음 단계
- [클래식에서 Azure Resource Manager로 IaaS 리소스 플랫폼 지원 마이그레이션 개요](../virtual-machines/migration-classic-resource-manager-overview.md)
- [Azure Portal](in-place-migration-portal.md)을 사용하여 Cloud Services(확장 지원)로 마이그레이션
- [PowerShell](in-place-migration-powershell.md)을 사용하여 Cloud Services(확장 지원)로 마이그레이션