---
title: Azure 보안 제품을 사용 하 여 security Center 통합 | Microsoft Docs
description: 이 항목에서는 Azure Security Center와 통합 되는 Azure 보안 제품을 표시 합니다.
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
ms.author: monhaber
ms.openlocfilehash: 805c770f1a7e9bb4e0619b27ac937a2451421dc6
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571739"
---
# <a name="security-center-integration-with-azure-security-products-in-asc"></a>ASC에서 Azure 보안 제품을 사용 하 여 security Center 통합

Security Center, 발견 한 Security Center에 등록 하려면 추가 Microsoft 라이선스를 사용 하 여 고객에 게를 제공 하 고 통합 된 방식으로 볼 합니다.

* [Azure WAF](#azure-waf)
* [Azure DDoS](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Azure Application Gateway는 일반적인 악용 및 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호하는 WAF(웹 애플리케이션 방화벽)를 제공합니다.

웹 응용 프로그램 점점 더 일반적으로 알려진된 취약성을 악용 하는 악의적 공격의 대상입니다. Application Gateway WAF는에서 핵심 규칙 집합 (CR) 3.0 또는 2.2.9의는 OWASP Open Web Application Security 프로젝트 ()를 기반 합니다. WAF는 추가 구성이 필요 없는 새로운 취약점 으로부터 보호 하기 위해 자동으로 업데이트 됩니다. WAF에서 생성 된 경고는 Security Center로 스트리밍됩니다. WAF에서 생성 된 경고에 대 한 자세한 내용은 참조 [문서](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911)합니다.

## Azure DDoS <a name="azure-ddos"></a>

분산된 서비스 거부 (DDoS) 공격 실행 쉬울 것으로 알려져 있습니다. 따라서 해당 응용 프로그램을 클라우드로 전환 하는 고객에 대 한 매우 유용한 보안 문제가 되는 됩니다. 

DDoS 공격은 애플리케이션의 리소스를 소진시켜서 정상적인 사용자가 애플리케이션을 사용할 수 없게 만듭니다. DDoS 공격은 인터넷을 통해 연결할 수 있는 모든 끝점을 지정할 수 있습니다.

응용 프로그램 설계 모범 사례와 결합 하 여 azure DDoS protection을 DDoS 공격에 대 한 방어를 제공 합니다. Azure DDoS protection 다양 한 서비스 계층을 제공합니다. 자세한 내용은 [Azure DDoS Protection 개요](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)합니다.

DDoS Protection 표준은 다음 유형의 공격을 완화할 수 있습니다.

> [!div class="mx-tableFixed"]

|경고|Description|
|---|---|
|**대규모 공격 감지 됨**|이 공격의 목표는 상당한 양의 트래픽 마비를 사용 하 여 네트워크 계층입니다. 여기에는 UDP 서비스 장애, 증폭 서비스 장애 및 기타 스푸핑된 패킷 서비스 장애가 포함됩니다. DDoS Protection 표준은 자동으로 Azure의 글로벌 네트워크 규모를 사용하여 이러한 잠재적인 멀티 기가바이트 공격을 흡수하고 스크럽하여 완화시킵니다.|
|**프로토콜 공격 감지 됨**|이러한 공격 대상을 액세스 불능를 계층 4 프로토콜 스택의 계층 3의에서 약점을 악용 하 여 합니다. SYN 서비스 장애 공격, 리플렉션 공격 및 기타 프로토콜 공격이 여기에 포함됩니다. DDoS Protection 표준은 클라이언트와의 상호 작용을 통해 악성 트래픽과 정상 트래픽을 구분하고 악성 트래픽을 차단하여 이러한 공격을 완화합니다.|
|**리소스 (응용 프로그램) 계층 공격 감지 됨**|이러한 공격은 대상 웹 응용 프로그램 패킷을 호스트 간 데이터 전송을 방해 합니다. 공격에는 HTTP 프로토콜 위반, SQL 주입, 교차 사이트 스크립팅 및 기타 계층 7 공격이 포함됩니다. DDoS Protection Standard를 사용 하 여 Azure Application Gateway 웹 응용 프로그램 방화벽을 사용 하 여 이러한 공격을 방어 하기. 또한 타사 웹 응용 프로그램 방화벽 제품 제공의 경우 Azure Marketplace|
