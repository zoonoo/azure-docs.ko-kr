---
title: 포함 파일
description: 포함 파일
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 8/13/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a63a12658bd0a4b4d018d51824af9814691a3cbf
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2018
ms.locfileid: "40182509"
---
### <a name="what-is-azure-firewall"></a>Azure Firewall이란?

Azure Firewall은 Azure Virtual Network 리소스를 보호하는 관리되는 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다. 구독 및 가상 네트워크 전반에 걸쳐 응용 프로그램 및 네트워크 연결 정책을 중앙에서 만들고, 적용하고 기록할 수 있습니다. Azure Firewall은 현재 공개 미리 보기로 제공됩니다.

### <a name="which-capabilities-are-supported-in-the-azure-firewall-public-preview-release"></a>Azure Firewall 미리 보기 릴리스에서 지원되는 기능은 무엇입니까?  

* 서비스로서의 상태 저장 방화벽
* 무제한 클라우드 확장성이 포함된 기본 제공 고가용성
* FQDN 필터링 
* 네트워크 트래픽 필터링 규칙
* 아웃바운드 SNAT 지원
* Azure 구독 및 가상 네트워크 전반에 걸쳐 응용 프로그램 및 네트워크 연결 정책을 중앙에서 만들고, 적용하고 기록하는 기능
* 로깅 및 분석을 위한 Azure Monitor와의 완전한 통합 

### <a name="how-can-i-join-the-azure-firewall-public-preview"></a>Azure Firewall 공개 미리 보기에 조인하는 방법

Azure Firewall은 현재 관리되는 공개 미리 보기로 제공되며, Register-AzureRmProviderFeature PowerShell 명령을 사용하여 조인할 수 있습니다. 이 명령은 Azure Firewall 공개 미리 보기 설명서에 설명되어 있습니다.

### <a name="what-is-the-pricing-for-azure-firewall"></a>Azure Firewall의 가격 책정은 어떻게 되나요?

Azure Firewall은 고정 비용 및 변동 비용이 있습니다. 가격은 다음과 같으며, 공개 미리 보기 기간에는 50% 할인됩니다.

* 고정 요금: $1.25/방화벽/시간당
* 변동 요금: $0.03/방화벽에서 처리하는 GB당(수신 또는 송신)

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Azure Firewall의 일반적인 배포 모델은 무엇입니까?

어떤 가상 네트워크에도 Azure Firewall을 배포할 수 있지만 고객은 일반적으로 중앙 가상 네트워크에 배포한 후, 허브 및 스포크 모델에서 다른 가상 네트워크를 중앙 가상 네트워크에 피어링합니다. 그 후 피어링된 가상 네트워크에서 이 중앙의 방화벽 가상 네트워크를 가리키도록 기본 경로를 설정할 수 있습니다.

### <a name="how-can-i-install-the-azure-firewall"></a>Azure Firewall을 설치하려면 어떻게 해야 하나요?

Azure Portal, PowerShell, REST API 또는 템플릿을 사용하여 Azure Firewall을 설정할 수 있습니다. 단계별 지침은 [자습서: Azure Portal을 사용하여 Azure Firewall 배포 및 구성](../articles/firewall/tutorial-firewall-deploy-portal.md)을 참조하세요.

### <a name="what-are-some-azure-firewall-concepts"></a>Azure Firewall의 몇 가지 개념을 알려주세요.

Azure Firewall은 규칙 및 규칙 컬렉션을 지원합니다. 규칙 컬렉션은 동일한 작업 및 우선 순위를 공유하는 규칙 집합입니다. 규칙 컬렉션은 우선 순위에 따라 실행됩니다. 네트워크 규칙 컬렉션이 응용 프로그램 규칙 컬렉션보다 우선 순위가 높으며, 모든 규칙은 종료됩니다.

다음과 같은 두 가지 유형의 규칙 컬렉션이 있습니다.

* *응용 프로그램 규칙*: 서브넷에서 액세스할 수 있는 정규화된 도메인 이름(FQDN)을 구성할 수 있습니다. 
* *네트워크 규칙* 원본 주소, 프로토콜, 대상 포트 및 대상 주소를 포함하는 규칙을 구성할 수 있습니다. 

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure Firewall은 인바운드 트래픽 필터링을 지원하나요?

Azure Firewall 공개 미리 보기는 아웃 바운드 필터링만 지원합니다. 비 HTTP/S 프로토콜(예: RDP, SSH 또는 FTP)에 대한 인바운드 보호는 Azure Firewall GA에서 제공할 예정입니다.  
 
### <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Azure Firewall에서는 어떤 로깅 및 분석 서비스를 지원하나요?

Azure Firewall은 방화벽 로그를 살펴보고 분석할 수 있도록 Azure Monitor와 통합됩니다. 로그를 Log Analytics, Azure Storage 또는 Event Hubs로 전송할 수 있습니다. 전송된 로그를 Log Analytics 또는 Excel이나 Power BI 같은 다른 도구에서 분석할 수 있습니다. 자세한 내용은 [자습서: Azure Firewall 로그 모니터링](../articles/firewall/tutorial-diagnostics.md)을 참조하세요.

### <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Azure Firewall이 Marketplace의 NVA와 같은 기존 서비스와 어떻게 다르게 작동하나요?

Azure Firewall은 특정 고객 시나리오를 해결할 수 있는 기본 방화벽 서비스입니다. 타사 NVA와 Azure Firewall이 혼합되어 있을 것으로 예상됩니다. 함께 잘 작동하는 것이 가장 중요합니다.
 
### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Application Gateway WAF와 Azure Firewall의 차이점은 무엇인가요?

WAF(웹 응용 프로그램 방화벽)는 일반적인 악용 및 취약점으로부터 웹 응용 프로그램에 대해 중앙 집중식 인바운드 보호를 제공하는 Application Gateway의 기능입니다. Azure Firewall은 모든 포트 및 프로토콜에 아웃바운드 네트워크 수준 보호를 제공하고 아웃바운드 HTTP/S에 응용 프로그램 수준 보호를 제공합니다. 비 HTTP/S 프로토콜(예: RDP, SSH, FTP)에 대한 인바운드 보호는 Azure Firewall GA에서 제공할 예정입니다.

### <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>NSG(네트워크 보안 그룹)와 Azure Firewall의 차이점은 무엇입니까?

Azure Firewall 서비스는 네트워크 보안 그룹 기능을 보완합니다. 그뿐 아니라 더 나은 "심층 방어" 네트워크 보안을 제공합니다. 네트워크 보안 그룹은 각 구독의 가상 네트워크 내 리소스에 대한 트래픽을 제한하는 분산 네트워크 레이어 트래픽 필터링을 제공합니다. Azure Firewall은 상태를 저장하는 서비스 형태의 완전한 중앙 집중식 네트워크 방화벽으로, 다양한 구독 및 가상 네트워크에 네트워크 및 응용 프로그램 수준의 보호를 제공합니다. 

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>서비스 엔드포인트를 사용하여 Azure Firewall을 설정하려면 어떻게 해야 하나요?

PaaS 서비스에 안전하게 액세스하려면 서비스 엔드포인트를 사용하는 것이 좋습니다. Azure Firewall 서브넷에서 서비스 엔드포인트를 사용하도록 설정하고, 연결된 스포크 가상 네트워크에서는 사용하지 않도록 설정하도록 선택할 수 있습니다. 이러한 방식으로 서비스 엔드포인트 보안과 모든 트래픽에 대한 중앙 로깅 기능의 이점을 모두 얻을 수 있습니다.

### <a name="how-can-i-stop-and-start-azure-firewall"></a>Azure Firewall을 중지하려면 어떻게 하나요?

Azure PowerShell *할 당 취소* 및 *할당* 메서드를 사용할 수 있습니다.

예: 

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzureRmFirewall -Name "FW Name” -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzureRmFirewall -AzureFirewall $azfw
```

```azurepowershell
#Start a firewall

$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzureRmPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzureRmFirewall -AzureFirewall $azfw
```

### <a name="what-are-the-known-service-limits"></a>알려진 서비스 제한 사항은 무엇입니까?

* Azure Firewall의 제한은 1000TB/방화벽/월입니다. 
* 중앙 가상 네트워크에서 실행되는 Azure Firewall 방화벽 인스턴스는 최대 50개스포크 가상 네트워크에 해당하는 가상 네트워크 피어링 제한이 적용됩니다.  
* Azure Firewall은 글로벌 피어링으로 작동하지 않으므로 고객은 지역당 최소 하나의 방화벽 배포가 필요합니다.
* Azure Firewall은 10,000개의 응용 프로그램 규칙과 10,000개의 네트워크 규칙을 지원합니다.