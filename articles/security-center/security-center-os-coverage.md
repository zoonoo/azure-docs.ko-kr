---
title: Azure 보안 센터에서 지원하는 플랫폼 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 보안 센터에서 지원하는 플랫폼 목록을 제공합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 3c8bf69b745f5dba8c08556908df4d4ae5b5769f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521910"
---
# <a name="supported-platforms"></a>지원 플랫폼 

이 페이지에는 Azure 보안 센터에서 지원하는 플랫폼 및 환경이 표시됩니다.

## <a name="combinations-of-environments"></a>환경의 조합<a name="vm-server"></a>

Azure Security Center는 다양한 유형의 하이브리드 환경에서 가상 컴퓨터와 서버를 지원합니다.

* Azure만
* Azure 및 온-프레미스
* Azure 및 기타 클라우드
* Azure, 기타 클라우드 및 온-프레미스

Azure 구독에서 활성화된 Azure 환경의 경우 Azure 보안 센터는 구독 내에 배포된 IaaS 리소스를 자동으로 검색합니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

보안 센터는 [로그 분석 에이전트에](../azure-monitor/platform/agents-overview.md#log-analytics-agent)따라 다릅니다. 다음 페이지에 설명된 대로 컴퓨터가 이 에이전트에 대해 지원되는 운영 체제 중 하나를 실행하고 있는지 확인합니다.

* [Windows 지원 운영 체제용 로그 분석 에이전트](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)
* [Linux 지원 운영 체제용 로그 분석 에이전트](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

또한 Log Analytics 에이전트가 [보안 센터로 데이터를 전송하도록 올바르게 구성되었는지](security-center-enable-data-collection.md#manual-agent) 확인합니다.

> [!TIP]
> Windows 및 Linux에서 사용할 수 있는 특정 보안 센터 기능에 대한 자세한 내용은 [컴퓨터에 대한 기능 범위를](security-center-services.md)참조하십시오.

## <a name="managed-virtual-machine-services"></a>관리형 가상 머신 서비스<a name="virtual-machine"></a>

가상 컴퓨터는 또한 Azure Kubernetes(AKS), Azure Databricks 등과 같은 일부 Azure 관리 서비스의 일부로 고객 구독에서 만들어집니다. 보안 센터에서도 이러한 가상 시스템을 검색하고 지원되는 OS를 사용할 수 있는 경우 Log Analytics 에이전트를 설치하고 구성할 수 있습니다.

## <a name="cloud-services"></a>클라우드 서비스<a name="cloud-services"></a>

클라우드 서비스에서 실행되는 가상 시스템도 지원됩니다. 프로덕션 슬롯에서 실행되는 클라우드 서비스 웹 및 작업자 역할만 모니터링됩니다. 클라우드 서비스에 대한 자세한 내용은 [Azure Cloud Services 개요](../cloud-services/cloud-services-choose-me.md)를 참조하세요.

Azure 스택에 있는 VM에 대 한 보호도 지원 됩니다. Azure 스택과 보안 센터의 통합에 대 한 자세한 내용은 [Azure 스택 가상 컴퓨터를 보안 센터에 온보보드](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)참조.

## <a name="next-steps"></a>다음 단계

- 보안 [센터에서 로그 분석 에이전트를 사용하여 데이터를 수집하는](security-center-enable-data-collection.md)방법에 대해 알아봅니다.
- 보안 [센터에서 데이터를 관리하고 보호하는](security-center-data-security.md)방법에 대해 알아보십시오.
- [Azure 보안 센터를 채택하기](security-center-planning-and-operations-guide.md)위한 디자인 고려 사항을 계획하고 이해하는 방법에 대해 알아봅니다.