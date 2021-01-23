---
title: Azure Cloud Services에 대 한 질문과 대답 (연장 지원)
description: Azure Cloud Services에 대 한 질문과 대답 (연장 지원)
ms.topic: conceptual
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 3338f7b6bd418cea2bfdbbcd40692b9342f48cfa
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744569"
---
# <a name="frequently-asked-questions-for-azure-cloud-services-extended-support"></a>Azure Cloud Services(추가 지원)에 대한 질문과 대답
이 문서에서는 Azure Cloud Services (확장 지원)와 관련 된 질문과 대답을 다룹니다.

## <a name="general"></a>일반

### <a name="what-is-the-resource-name-for-cloud-services-classic--cloud-services-extended-support"></a>Cloud Services (클래식) & Cloud Services (확장 지원)에 대 한 리소스 이름은 무엇 인가요?
- Cloud Services (클래식): `microsoft.classiccompute/domainnames`
- Cloud Services (확장 지원): `microsoft.compute/cloudservices`

### <a name="what-locations-are-available-to-deploy-cloud-services-extended-support"></a>Cloud Services (확장 지원)을 배포 하는 데 사용할 수 있는 위치
모든 공용 클라우드 지역에서 Cloud Services (확장 지원)을 사용할 수 있습니다.

### <a name="how-does-my-quota-change"></a>할당량이 어떻게 변경 되나요? 
고객은 다른 Azure Resource Manager 제품과 동일한 프로세스를 사용 하 여 할당량을 요청 해야 합니다. Azure Resource Manager 할당량은 지역 이며 각 지역에 별도의 할당량 요청이 필요 합니다.

### <a name="why-dont-i-see-a-production--staging-slot-anymore"></a>프로덕션 & 스테이징 슬롯이 더 이상 표시 되지 않는 이유는 무엇 인가요?
Cloud Services (확장 지원)는 두 개의 슬롯 (프로덕션 & 준비)을 포함 하는 호스 티 드 서비스의 논리적 개념을 지원 하지 않습니다. 각 배포는 독립 된 클라우드 서비스 (확장 지원) 배포입니다. 클라우드 서비스의 새 릴리스를 테스트 하 고 준비 하려면 클라우드 서비스 (확장 된 지원)를 배포 하 고 다른 클라우드 서비스 (확장 지원)를 사용 하 여 VIP로 스왑로 태그를 추가 합니다.

### <a name="why-cant-i-create-an-empty-cloud-service-anymore"></a>빈 클라우드 서비스를 더 이상 만들 수 없는 이유는 무엇입니까?
호스팅된 서비스 이름의 개념이 더 이상 존재 하지 않습니다. 빈 클라우드 서비스 (확장 지원)를 만들 수 없습니다.

### <a name="does-cloud-services-extended-support-support-resource-health-check-rhc"></a>Cloud Services (확장 지원) 지원 Resource Health 확인 (RHC)을 지원 하나요?
아니요, Cloud Services (확장 지원)는 Resource Health 확인 (RHC)을 지원 하지 않습니다.

### <a name="how-are-role-instance-metrics-changing"></a>역할 인스턴스 메트릭은 어떻게 변경 되나요?
역할 인스턴스 메트릭에는 변경 내용이 없습니다. 

### <a name="how-are-web--worker-roles-changing"></a>웹 & 작업자 역할은 어떻게 변경 되나요?
웹 및 작업자 역할의 디자인, 아키텍처 또는 구성 요소에는 변경 내용이 없습니다. 

### <a name="how-are-role-instances-changing"></a>역할 인스턴스는 어떻게 변경 되나요?
역할 인스턴스의 디자인, 아키텍처 또는 구성 요소에는 변경 내용이 없습니다. 

### <a name="how-will-guest-os-updates-change"></a>게스트 os 업데이트는 어떻게 변경 되나요?
 롤아웃 방법에 대 한 변경 사항은 없습니다. Cloud Services (클래식) 및 Cloud Services (확장 지원)는 동일한 업데이트를 받습니다.
 
### <a name="does-cloud-services-extended-support-support-stopped-allocated-and-stopped-deallocated-states"></a>Cloud Services (확장 지원)는 중지 할당 및 중지 된 할당 취소 상태를 지원 하나요?

Cloud Services (확장 지원) 배포는 Azure Portal에서 "중지 됨"으로 표시 되는 중지 된 할당 상태만 지원 합니다. 중지 됨-할당 취소 된 상태는 지원 되지 않습니다. 

### <a name="do-cloud-services-extended-support-deployments-support-scaling-across-clusters-availability-zones-and-regions"></a>Cloud Services (확장 지원) 배포는 클러스터, 가용성 영역 및 지역 전체의 확장을 지원 하나요?
Cloud Services (확장 지원) 배포는 여러 클러스터, 가용성 영역 및 지역에서 확장할 수 없습니다. 

### <a name="are-there-any-pricing-differences-between-cloud-services-classic-and-cloud-services-extended-support"></a>Cloud Services (클래식)와 Cloud Services (확장 지원) 간의 가격 차이가 있나요?
Cloud Services (확장 지원)는 ARM (Azure Key Vault 및 기본) 공용 IP 주소를 사용 합니다.인증서를 요구 하는 고객은 인증서 관리를 위해 Azure Key Vault를 사용 해야 합니다 (Azure Key Vault 가격 책정에 대해[자세히 알아보세요](https://azure.microsoft.com/pricing/details/key-vault/) ).   Cloud Services (확장 지원)에 대 한 각 공용 IP 주소는 별도로 청구 됩니다 (공용 IP 주소 가격에 대 한[자세한](https://azure.microsoft.com/pricing/details/ip-addresses/) 정보). 
## <a name="resources"></a>리소스 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-resource-group"></a>Cloud Services (확장 지원) 배포에 연결 된 리소스는 동일한 리소스 그룹 내에 있어야 합니다.
부하 분산 장치, 네트워크 보안 그룹 및 경로 테이블은 동일한 지역 및 리소스 그룹에 있어야 합니다. 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-region"></a>Cloud Services (확장 지원) 배포에 연결 된 리소스는 동일한 지역에 있어야 하나요?
Key Vault, 가상 네트워크, 공용 IP 주소, 네트워크 보안 그룹 및 경로 테이블은 동일한 지역에 있어야 합니다.

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-virtual-network"></a>Cloud Services (확장 지원) 배포에 연결 된 리소스는 동일한 가상 네트워크에 있어야 합니다.
공용 IP 주소, 부하 분산 장치, 네트워크 보안 그룹 및 경로 테이블은 동일한 가상 네트워크에 있어야 합니다. 

## <a name="deployment-files"></a>배포 파일 

### <a name="how-can-i-use-a-template-to-deploy-or-manage-my-deployment"></a>템플릿을 사용 하 여 배포를 배포 하거나 관리 하려면 어떻게 해야 하나요?
REST, PowerShell 및 CLI를 사용 하 여 템플릿 및 매개 변수 파일을 매개 변수로 전달할 수 있습니다. 또한 Azure Portal를 사용 하 여 업로드할 수 있습니다.  

### <a name="do-i-need-to-maintain-four-files-now-template-parameter-csdef-cscfg"></a>4 개의 파일을 유지 관리 해야 하나요? (템플릿, 매개 변수, .csdef, .cscfg)
템플릿 및 매개 변수 파일은 배포 자동화에만 사용 됩니다. Cloud Services (클래식) 처럼 먼저 종속 리소스를 수동으로 만든 다음 PowerShell, CLI 명령 또는 기존 .csdef, .cscfg를 사용 하는 포털을 통해 Cloud Services (확장 지원) 배포를 수행할 수 있습니다.

### <a name="how-does-my-application-code-change-on-cloud-services-extended-support"></a>Cloud Services에서 응용 프로그램 코드를 변경 하는 방법 (확장 지원)
Cspkg에 패키지 된 응용 프로그램 코드에 필요한 변경 사항은 없습니다. 기존 응용 프로그램은 이전 처럼 계속 작동 합니다. 


## <a name="migration"></a>마이그레이션

### <a name="will-cloud-services-extended-support-mitigate-the-failures-due-to-allocation-failures"></a>할당 오류로 인 한 실패를 완화 (확장 지원) Cloud Services 합니다.
아니요, 클라우드 서비스 (확장 지원) 배포는 Cloud Services (클래식)와 같은 클러스터에 연결 됩니다. 따라서 클러스터가 가득 찬 경우에는 할당 실패가 계속 존재 합니다. 

### <a name="when-do-i-need-to-migrate"></a>마이그레이션이 필요한 경우 
필요한 시간을 예측 하 고 복잡성을 예측 하는 것은 변수의 범위에 따라 다릅니다. 계획은 작업의 범위, 차단 및 마이그레이션 복잡성을 이해 하는 가장 효과적인 단계입니다.

## <a name="networking"></a>네트워킹

### <a name="why-cant-i-create-a-deployment-without-virtual-network"></a>가상 네트워크가 없는 배포를 만들 수 없는 이유는 무엇입니까?
가상 네트워크는 Azure Resource Manager에 배포 하는 데 필요한 리소스입니다. Cloud Services (확장 지원) 배포는 가상 네트워크 내에 있어야 합니다. 

### <a name="why-am-i-now-seeing-so-many-networking-resources"></a>이제 많은 네트워킹 리소스가 표시 되는 이유는 무엇 인가요? 
Azure Resource Manager에서는 향상 된 가시성과 향상 된 제어를 위해 Cloud Services (확장 된 지원) 배포의 구성 요소가 리소스로 노출 됩니다. 동일한 유형의 리소스가 Cloud Services (클래식)에서 사용 되었지만 숨겨져 있습니다. 이러한 리소스의 한 가지 예는 공용 Load Balancer입니다 .이는 이제 플랫폼에서 자동으로 생성 된 명시적인 ' 읽기 전용 ' 리소스입니다.

### <a name="what-restrictions-apply-for-a-subnet-with-respective-to-cloud-services-extended-support"></a>Cloud Services에 대 한 서브넷에 적용 되는 제한 사항 (확장 지원)
Cloud Services (확장 지원) 배포를 포함 하는 서브넷은 Virtual Machines, Virtual Machines 크기 집합, Service Fabric 등과 같은 다른 계산 제품의 배포와 공유할 수 없습니다.

### <a name="what-ip-allocation-methods-are-supported-on-cloud-services-extended-support"></a>클라우드 서비스에서 지원 되는 IP 할당 방법 (연장 된 지원)은 무엇 인가요?
Cloud Services (확장 지원)는 동적 & 고정 IP 할당 방법을 지원 합니다. 고정 IP 주소는 .cscfg 파일에 예약 된 ip로 참조 됩니다.

### <a name="why-am-i-getting-charged-for-ip-addresses"></a>IP 주소에 대 한 요금이 청구 되는 이유는 무엇 인가요?
사용자에 게는 가상 컴퓨터와 연결 된 IP 주소에 대 한 요금이 청구 되는 것 처럼 Cloud Services (확장 지원)에서 IP 주소 사용에 대 한 요금이 청구 됩니다. 

### <a name="can-i-use-a-dns-name-with-cloud-services-extended-support"></a>Cloud Services (확장 지원)에서 DNS 이름을 사용할 수 있나요? 
예. Cloud Services (확장 지원)에 DNS 이름을 지정할 수도 있습니다. Azure Resource Manager DNS 레이블은 클라우드 서비스에 할당 된 공용 IP 주소의 선택적 속성입니다. Azure Resource Manager 기반 배포에 대 한 DNS 이름 형식은입니다. `<userlabel>.<region>.cloudapp.azure.com`

## <a name="certificates--key-vault"></a>인증서 & Key Vault

### <a name="why-do-i-need-to-manage-my-certificates-on-cloud-services-extended-support"></a>Cloud Services (확장 지원)에서 내 인증서를 관리 해야 하는 이유는 무엇 인가요?
Cloud Services (확장 지원)는 고객이 관리 하는 주요 자격 증명 모음 내에 인증서가 있는 다른 계산 제품으로 동일한 프로세스를 채택 했습니다. 이를 통해 고객은 암호 & 인증서를 완전히 제어할 수 있습니다. 

### <a name="can-i-use-one-key-vault-for-all-my-deployments-in-all-regions"></a>모든 지역의 모든 배포에 대해 하나의 Key Vault 사용할 수 있나요?
아니요. Key Vault은 지역 리소스 이며, 고객은 각 지역에 하나의 Key Vault 필요 합니다. 그러나 지정 된 지역 내의 모든 배포에 대해 하나의 Key Vault 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Cloud Services (확장 지원) 사용을 시작 하려면 [PowerShell을 사용 하 여 클라우드 서비스 배포 (확장 지원)](deploy-powershell.md) 를 참조 하세요.