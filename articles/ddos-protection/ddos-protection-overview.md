---
title: Azure DDoS Protection 표준 개요
description: 애플리케이션 설계 모범 사례와 결합된 Azure DDoS Protection 표준이 DDoS 공격에 대한 방어 기능을 제공하는 방법을 알아보세요.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: e3ded2fc286117da1438b0bb28298632532c4329
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992542"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS Protection 표준 개요

DDoS(배포된 서비스 거부) 공격은 고객이 애플리케이션을 클라우드로 전환하게 만드는 가장 큰 가용성 및 보안 문제 중 일부입니다. DDoS 공격은 애플리케이션의 리소스를 소진시켜서 정상적인 사용자가 애플리케이션을 사용할 수 없게 만듭니다. 인터넷을 통해 공개적으로 도달 가능한 모든 엔드포인트는 DDoS 공격의 대상이 될 수 있습니다.

Azure의 모든 속성은 추가 비용 없이 Azure의 인프라 DDoS (기본) 보호를 통해 보호 됩니다. 전역 배포 Azure 네트워크의 규모 및 용량은 항상 작동하는 트래픽 모니터링 및 실시간 위험 완화를 통해 일반적인 네트워크 레이어 공격을 방어합니다. DDoS Protection 기본에는 사용자 구성 또는 애플리케이션 변경이 필요하지 않습니다. DDoS Protection 기본을 통해 Azure DNS 같은 PaaS 서비스를 포함한 모든 Azure 서비스를 보호할 수 있습니다.

응용 프로그램 설계 모범 사례와 결합 된 Azure DDoS Protection 표준은 DDoS 공격 으로부터 보호 하는 향상 된 DDoS 완화 기능을 제공 합니다. 가상 네트워크의 특정 Azure 리소스를 보호 하도록 자동으로 조정 됩니다. 보호는 새 가상 네트워크 또는 기존 가상 네트워크에서 간단하게 설정할 수 있으며 애플리케이션 또는 리소스를 변경할 필요가 없습니다. 로깅, 경고 및 원격 분석을 포함하여 기본 서비스에 비해 여러 가지 장점이 있습니다. 

![Azure DDoS Protection 서비스 비교](./media/ddos-protection-overview/ddos-comparison.png)

Azure DDoS protection은 고객 데이터를 저장 하지 않습니다.

## <a name="features"></a>기능

- **네이티브 플랫폼 통합:** 기본적으로 Azure에 통합됩니다. Azure Portal을 통해 구성을 포함합니다. DDoS Protection 표준은 사용자의 리소스 및 리소스 구성을 인식합니다.
- **턴키 보호:** 단순화 된 구성은 DDoS Protection Standard를 사용 하도록 설정 하는 즉시 가상 네트워크의 모든 리소스를 즉시 보호 합니다. 작업 또는 사용자 정의가 필요하지 않습니다. DDoS Protection 표준은 공격이 감지되는 즉시 자동으로 공격을 완화시킵니다.
- **상시 트래픽 모니터링:** 응용 프로그램 트래픽 패턴은 DDoS 공격에 대 한 지표를 찾고 하루 24 시간 동안 모니터링 됩니다. 보호 정책이 초과되면 완화가 수행됩니다.
- **적응 조정:** 지능형 트래픽 프로 파일링은 시간에 따른 응용 프로그램의 트래픽을 학습 하 고 서비스에 가장 적합 한 프로필을 선택 하 여 업데이트 합니다. 트래픽이 시간이 지남에 따라 변경되면서 프로필이 조정됩니다.
- **다중 계층 보호:** 웹 애플리케이션 방화벽과 함께 사용될 경우 전체 스택 DDoS 보호를 제공합니다.
- **광범위한 완화 규모:** 가장 큰 규모로 알려진 DDoS 공격으로부터 시스템을 보호할 수 있는 글로벌 역량으로 60가지 공격을 완화할 수 있습니다.
- **공격 분석:** 공격 진행 중에 5분 단위로 세부 보고서를 가져오고, 공격이 종료된 후에는 전체 요약을 가져옵니다. 공격을 거의 실시간으로 모니터링 하기 위해 [Azure 센티널](../sentinel/connect-azure-ddos-protection.md) 또는 오프 라인 siem (보안 정보 및 이벤트 관리) 시스템으로 마이그레이션 완화 흐름 로그
- **공격 메트릭:** Azure Monitor를 통해 각 공격으로부터 요약된 메트릭에 액세스할 수 있습니다.
- **공격 경고:** 경고는 공격 시작 및 중지 시, 그리고 공격이 진행 되는 동안 기본 제공 공격 메트릭을 사용 하 여 구성할 수 있습니다. 경고는 Microsoft Azure 모니터 로그, Splunk, Azure Storage, 전자 메일 및 Azure Portal와 같은 운영 소프트웨어에 통합 됩니다.
- **DDoS 신속한 응답**: 공격 조사 및 분석에 도움이 되도록 drr (DDoS Protection 빠른 응답) 팀에 참여 하세요. 자세히 알아보려면 [DDoS 신속한 응답](ddos-rapid-response.md)을 참조 하세요.
- **비용 보장:** 문서화된 DDoS 공격에 대한 데이터 전송 및 애플리케이션 확장 서비스 크레딧이 제공됩니다.

## <a name="pricing"></a>가격 책정

Azure DDoS Protection 표준 가격 책정에 대 한 자세한 내용은 [Azure DDoS Protection 표준 가격 책정](https://azure.microsoft.com/pricing/details/ddos-protection/)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [DDoS Protection 계획 만들기](manage-ddos-protection.md)