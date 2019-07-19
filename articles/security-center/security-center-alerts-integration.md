---
title: Azure 보안 제품과 Security Center 통합 | Microsoft Docs
description: 이 항목에서는 Azure Security Center와 통합 된 Azure 보안 제품을 제공 합니다.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 64a636cc4452de1ef4a2d0e94629e7d8e3a5878d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295747"
---
# <a name="security-center-integration-with-azure-security-products-in-asc"></a>ASC의 Azure 보안 제품과 Security Center 통합

Security Center은 고객에 게 결과를 등록 하 여 Security Center 하 고 통합 된 방식으로 볼 수 있는 추가 Microsoft 라이선스를 제공 합니다.

* [Azure WAF](#azure-waf)
* [Azure DDoS](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Azure Application Gateway는 일반적인 악용 및 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호하는 WAF(웹 애플리케이션 방화벽)를 제공합니다.

웹 응용 프로그램은 일반적으로 알려진 취약점을 악용 하는 악의적인 공격을 대상으로 점차 증가 하 고 있습니다. Application Gateway WAF는 OWASP (Open Web Application Security Project)의 CRS (핵심 규칙 집합) 3.0 또는 2.2.9을 기반으로 합니다. WAF는 추가 구성 없이도 새로운 취약점 으로부터 보호 하기 위해 자동으로 업데이트 됩니다. WAF에 의해 생성 된 경고는 Security Center로 스트리밍됩니다. WAF에 의해 생성 되는 경고에 대 한 자세한 내용은이 [문서](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911)를 참조 하세요.

## Azure DDoS <a name="azure-ddos"></a>

DDoS (분산 서비스 거부) 공격을 쉽게 실행 하는 것으로 알려져 있습니다. 따라서 응용 프로그램을 클라우드로 이동 하는 고객의 경우 보안이 매우 중요 합니다. 

DDoS 공격은 애플리케이션의 리소스를 소진시켜서 정상적인 사용자가 애플리케이션을 사용할 수 없게 만듭니다. DDoS 공격은 인터넷을 통해 도달할 수 있는 모든 끝점을 대상으로 지정할 수 있습니다.

응용 프로그램 설계 모범 사례와 결합 된 Azure DDoS protection은 DDoS 공격에 대 한 방어를 제공 합니다. Azure DDoS protection은 다양 한 서비스 계층을 제공 합니다. 자세한 내용은 [Azure DDoS Protection 개요](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)를 참조 하세요.

DDoS Protection 표준은 다음 유형의 공격을 완화할 수 있습니다.

> [!div class="mx-tableFixed"]

|경고|Description|
|---|---|
|**대규모 공격 감지 됨**|이 공격의 목표는 상당한 양의 합법적인 트래픽으로 네트워크 계층을 초과 하는 것입니다. 여기에는 UDP 서비스 장애, 증폭 서비스 장애 및 기타 스푸핑된 패킷 서비스 장애가 포함됩니다. DDoS Protection 표준은 자동으로 Azure의 글로벌 네트워크 규모를 사용하여 이러한 잠재적인 멀티 기가바이트 공격을 흡수하고 스크럽하여 완화시킵니다.|
|**프로토콜 공격 감지 됨**|이러한 공격은 계층 3 및 계층 4 프로토콜 스택의 취약점을 악용 하 여 대상을 액세스할 수 없게 렌더링 합니다. SYN 서비스 장애 공격, 리플렉션 공격 및 기타 프로토콜 공격이 여기에 포함됩니다. DDoS Protection 표준은 클라이언트와의 상호 작용을 통해 악성 트래픽과 정상 트래픽을 구분하고 악성 트래픽을 차단하여 이러한 공격을 완화합니다.|
|**리소스 (응용 프로그램) 계층 공격 감지**|이러한 공격은 웹 응용 프로그램 패킷을 대상으로 하 여 호스트 간 데이터 전송을 방해 합니다. 공격에는 HTTP 프로토콜 위반, SQL 주입, 교차 사이트 스크립팅 및 기타 계층 7 공격이 포함됩니다. Azure 애플리케이션 Gateway 웹 응용 프로그램 방화벽을 사용 하 여 DDoS Protection 표준을 통해 이러한 공격 으로부터 보호 합니다. Azure Marketplace에서 사용할 수 있는 타사 웹 응용 프로그램 방화벽 제품도 있습니다.|
