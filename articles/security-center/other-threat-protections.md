---
title: Azure Security Center의 추가 위협 방지
description: Azure Security Center에서 Azure Defender 이외에 사용 가능한 다른 위협 방지에 대해 알아보기
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: overview
ms.date: 09/15/2020
ms.author: memildin
ms.openlocfilehash: 63b93db314701d281f3f4fff195671f43cdb9dbc
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340889"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Azure Security Center의 추가 위협 방지
또한 Azure Security Center는 기본 제공 [Azure Defender 보호](azure-defender.md)뿐만 아니라 다음과 같은 위협 방지 기능도 제공합니다.

> [!TIP]
> Security Center의 위협 방지 기능을 사용하도록 설정하려면 해당 워크로드가 포함된 구독에서 Azure Defender를 사용하도록 설정해야 합니다.
>
> **Azure Database for MariaDB/MySQL/PostgreSQL**에 대한 위협 방지는 리소스 수준에서만 사용하도록 설정할 수 있습니다.


## <a name="threat-protection-for-azure-network-layer"></a>Azure 네트워크 계층에 대한 위협 방지 <a name="network-layer"></a>
Security Center 네트워크 계층 분석은 Azure 핵심 라우터에서 수집하는 패킷 헤더인 [IPFIX 데이터](https://en.wikipedia.org/wiki/IP_Flow_Information_Export) 샘플을 기반으로 합니다. 이 데이터 피드를 기반으로 하여 Security Center에서 기계 학습 모델을 사용하여 악성 트래픽 활동을 식별하고 플래그를 지정합니다. 또한 Security Center에서 Microsoft 위협 인텔리전스 데이터베이스를 사용하여 IP 주소를 보강합니다.

일부 네트워크 구성에서는 Security Center에서 의심스러운 네트워크 활동에 대한 경고를 생성하지 않도록 제한할 수 있습니다. Security Center에서 네트워크 경고를 생성하려면 다음을 확인합니다.
- 가상 머신에서 공용 IP 주소를 사용하거나 가상 머신이 공용 IP 주소가 있는 부하 분산 장치에 있습니다.
- 가상 머신의 네트워크 송신 트래픽은 외부 IDS 솔루션에서 차단되지 않습니다.
- 의심스러운 통신이 발생한 전체 시간 동안 동일한 IP 주소가 가상 머신에 할당되었습니다. 이는 관리형 서비스(예: AKS, Databricks)의 일부로 만든 VM에도 적용됩니다.

Azure 네트워크 계층 경고 목록은 [경고 참조 표](alerts-reference.md#alerts-azurenetlayer)를 참조하세요.


## <a name="threat-protection-for-azure-resource-manager-preview"></a>Azure Resource Manager에 대한 위협 방지(미리 보기)<a name ="management-layer"></a>
Azure Resource Manager를 기반으로 하는 Security Center의 보호 계층은 현재 미리 보기에 있습니다.

Security Center는 Azure Resource Manager 이벤트를 사용하여 추가 보호 계층을 제공하며, 이는 Azure의 제어 평면으로 간주됩니다. Security Center는 Azure Resource Manager 레코드를 분석하여 Azure 구독 환경에서 비정상적이거나 잠재적으로 유해한 작업을 탐지합니다.

Azure Resource Manager(미리 보기) 경고 목록은 [경고 참조 표](alerts-reference.md#alerts-azureresourceman)를 참조하세요.


>[!NOTE]
> 이전 분석의 일부는 Microsoft Cloud App Security에서 작동됩니다. 이러한 분석을 활용하려면 Cloud App Security 라이선스에 대한 정품 인증을 수행해야 합니다. Cloud App Security 라이선스가 있는 경우 이러한 경고는 기본적으로 사용하도록 설정됩니다. 경고를 사용하지 않도록 설정하려면 다음을 수행합니다.
>
> 1. Security Center 메뉴에서 **가격 책정 및 설정**을 선택합니다.
> 1. 변경하려는 구독을 선택합니다.
> 1. **위협 탐지**를 선택합니다.
> 1. **Microsoft Cloud App Security에서 내 데이터에 액세스하도록 허용합니다.** 를 선택 취소한 다음, **저장**을 선택합니다.


>[!NOTE]
>Security Center는 보안과 관련된 고객 데이터를 리소스와 동일한 지역에 저장합니다. Microsoft에서 Security Center를 해당 리소스의 지역에 아직 배포하지 않은 경우 데이터는 미국에 저장됩니다. Cloud App Security를 사용하도록 설정되는 경우 이 정보는 Cloud App Security의 지리적 위치 규칙에 따라 저장됩니다. 자세한 내용은 [비지역 서비스에 대한 데이터 저장](https://azuredatacentermap.azurewebsites.net/)을 참조하세요.

1. 에이전트를 설치할 작업 영역을 설정합니다. 작업 영역이 Security Center에서 사용하는 구독과 동일한 구독에 있어야 하고 작업 영역에 대한 읽기/쓰기 권한이 있어야 합니다.

1. **Azure Defender**를 사용하도록 설정하고 **저장**을 선택합니다.


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Azure Cosmos DB용 위협 방지(미리 보기)<a name="cosmos-db"></a>

Azure Cosmos DB 경고는 Azure Cosmos DB 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도로 인해 생성됩니다.

자세한 내용은 다음을 참조하세요.

* [Azure Cosmos DB용 Advanced Threat Protection(미리 보기)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB에 대한 위협 방지 경고 목록(미리 보기)](alerts-reference.md#alerts-azurecosmos)



## <a name="threat-protection-for-other-microsoft-services"></a>기타 Microsoft 서비스에 대한 위협 방지 <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Azure WAF에 대한 위협 방지 <a name="azure-waf"></a>

Azure Application Gateway는 일반적인 악용 및 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호하는 WAF(웹 애플리케이션 방화벽)를 제공합니다.

일반적으로 알려진 취약성을 악용하여 웹 애플리케이션을 공격하는 악의적인 사례가 점점 늘어나고 있습니다. Application Gateway WAF는 Open Web Application Security Project의 핵심 규칙 집합 3.0 또는 2.2.9를 기반으로 합니다. WAF는 새로운 취약성으로부터 보호하기 위해 자동으로 업데이트됩니다. 

Azure WAF에 대한 라이선스가 있는 경우 추가 구성 없이 WAF 경고가 Security Center로 스트림됩니다. WAF에서 생성되는 경고에 대한 자세한 내용은 [웹 애플리케이션 방화벽 CRS 규칙 그룹 및 규칙](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)을 참조하세요.


### <a name="threat-protection-for-azure-ddos-protection"></a>Azure DDoS Protection에 대한 위협 방지 <a name="azure-ddos"></a>

DDoS(분산 서비스 거부) 공격은 쉽게 실행되는 것으로 알려져 있습니다. 특히 애플리케이션을 클라우드로 이동하는 경우 매우 중요한 보안 문제가 됩니다. 

DDoS 공격은 애플리케이션의 리소스를 소진시켜서 정상적인 사용자가 애플리케이션을 사용할 수 없게 만듭니다. DDoS 공격은 인터넷을 통해 연결할 수 있는 모든 엔드포인트를 대상으로 할 수 있습니다.

DDoS 공격을 방어하려면 Azure DDoS Protection 라이선스를 구매하고 애플리케이션 디자인 모범 사례를 따릅니다. DDoS Protection은 다양한 서비스 계층을 제공합니다. 자세한 내용은 [Azure DDoS Protection 개요](../virtual-network/ddos-protection-overview.md)를 참조하세요.

Azure DDoS Protection 경고 목록은 [경고 참조 표](alerts-reference.md#alerts-azureddos)를 참조하세요.


## <a name="next-steps"></a>다음 단계
이러한 위협 방지 기능의 보안 경고에 대한 자세한 내용은 다음 문서를 참조하세요.

* [모든 Azure Security Center 경고 참조 표](alerts-reference.md)
* [Azure Security Center의 보안 경고](security-center-alerts-overview.md)
* [Azure Security Center에서 보안 경고 관리 및 응답](security-center-managing-and-responding-alerts.md)
* [보안 경고 및 추천 사항 내보내기(미리 보기)](continuous-export.md)