---
title: Azure 보안 센터 FAQ - 일반 질문
description: 위협을 방지, 탐지 및 대응하는 데 도움이 되는 제품인 Azure Security Center에 대해 자주 묻는 일반적인 질문
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: f9043ae1414b63f25583d52100774f3e87754a74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661842"
---
# <a name="faq---general-questions-about-azure-security-center"></a>FAQ - Azure 보안 센터에 대한 일반적인 질문

## <a name="what-is-azure-security-center"></a>Azure Security Center란?
Azure Security Center를 사용하면 리소스 보안에 대한 가시성을 높이고 제어하여 위협을 방지, 탐지 및 대응할 수 있습니다. 이는 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 보안 솔루션의 광범위한 환경에서 작동합니다.

보안 센터는 Microsoft 모니터링 에이전트를 사용하여 데이터를 수집하고 저장합니다. 자세한 내용은 [Azure 보안 센터의 데이터 수집을](security-center-enable-data-collection.md)참조하십시오.


## <a name="how-do-i-get-azure-security-center"></a>Azure Security Center를 이용하려면 어떻게 해야 하나요?
Azure Security Center는 Microsoft Azure 구독을 사용하도록 설정되어 있으며 [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에서 액세스합니다. 액세스하려면 [포털에 로그인하고](https://portal.azure.com) **찾아보기를**선택하고 보안 **센터로**스크롤합니다.


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Azure Security Center에서는 어떤 Azure 리소스를 모니터링하나요?
Azure Security Center에서는 다음과 같은 Azure 리소스를 모니터링합니다.

* 가상 시스템(VM) [(클라우드 서비스](../cloud-services/cloud-services-choose-me.md)포함)
* 가상 머신 크기 집합
* Azure Virtual Networks
* 컨테이너
* Azure SQL 서비스
* Azure Storage 계정
* Azure Web Apps([App Service Environment](../app-service/environment/intro.md))
* VM 및 App Service Environment에서 웹 애플리케이션 방화벽 같이 Azure 구독과 통합된 파트너 솔루션

또한 Azure 이외의(온-프레미스 포함) 컴퓨터를 Azure 보안 센터에서 모니터링할 수도 있습니다. [Windows 컴퓨터와](./quick-onboard-windows-computer.md) [Linux 컴퓨터가](./quick-onboard-linux-computer.md) 모두 지원됩니다.


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>내 Azure 리소스의 현재 보안 상태를 확인하려면 어떻게 해야 하나요?
**보안 센터 개요** 페이지에는 컴퓨팅, 네트워킹, 스토리지 & 데이터 및 응용 프로그램으로 세분화된 환경의 전반적인 보안 상태를 보여 줍니다. 각 리소스 유형에는 식별된 보안 취약점을 보여 주시는 표시등이 있습니다. 각 타일을 클릭하면 구독의 리소스 인벤토리와 함께 Security Center에서 식별하는 보안 문제 목록이 표시됩니다.



## <a name="what-is-a-security-policy"></a>보안 정책이란?
보안 정책은 지정된 구독 내에서 리소스에 대해 권장되는 제어 집합을 정의합니다. Azure Security Center에서 회사의 보안 요구 사항 및 애플리케이션 형식 또는 각 구독의 데이터 민감도에 따라 Azure 구독에 대한 정책을 정의합니다.

Azure Security Center에서 사용하도록 설정한 보안 정책에 따라 보안 권장 사항과 모니터링이 결정됩니다. 보안 정책에 대해 자세히 알아보려면 [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md)을 참조하세요.


## <a name="who-can-modify-a-security-policy"></a>보안 정책을 누가 수정할 수 있나요?
보안 정책을 수정하려면 해당 구독의 보안 관리자이거나 소유자 또는 참가자여야 합니다.

보안 정책을 구성하는 방법을 자세히 알아보려면 [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md)을 참조하세요.


## <a name="what-is-a-security-recommendation"></a>보안 권장 사항이란?
Azure Security Center에서는 Azure 리소스의 보안 상태를 분석합니다. 잠재적인 보안 취약성이 식별되면 권장 사항이 생성됩니다. 권장 사항은 필요한 컨트롤을 구성하는 과정을 안내합니다. 예:

* 악성 소프트웨어를 식별하여 제거하는 데 도움을 주는 맬웨어 방지 프로그램 프로비전
* 가상 머신의 트래픽을 제어하는 [네트워크 보안 그룹](../virtual-network/security-overview.md) 및 규칙
* 웹 애플리케이션의 대상을 지정한 공격에 대해 방어하는 데 도움이 되는 웹 애플리케이션 방화벽 프로비전
* 누락된 시스템 업데이트 배포
* 권장 기준과 일치하지 않는 OS 구성 해결

보안 정책에 사용하도록 설정된 권장 사항만 여기에 표시됩니다.



## <a name="what-triggers-a-security-alert"></a>보안 경고를 트리거하는 것은 무엇인가요?
Azure Security Center는 리소스, 네트워크 및 맬웨어 방지 프로그램과 방화벽 같은 파트너 솔루션에서 자동으로 로그 데이터를 수집, 분석 및 결합합니다. 위협이 감지되었을 때 보안 경고가 생성됩니다. 감지되는 사항의 예:

* 알려진 악성 IP 주소와 통신하는 손상된 가상 머신
* Windows 오류 보고를 사용 하여 감지된 고급 맬웨어
* 가상 머신에 대한 무작위 공격
* 맬웨어 방지 프로그램 또는 웹 애플리케이션 방화벽 등과 같은 통합된 파트너 보안 솔루션에서의 보안 경고


## <a name="why-did-secure-score-values-change"></a>보안 점수 값이 변경된 이유는 무엇입니까? <a name="secure-score-faq"></a>
2019년 2월부터 보안 센터는 심각도에 더 잘 부합하도록 몇 가지 권장 사항의 점수를 조정했습니다. 이 조정의 결과로 전체 보안 점수 값이 변경될 수 있습니다.  보안 점수에 대한 자세한 내용은 [보안 점수 계산을](security-center-secure-score.md)참조하십시오.


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Microsoft 보안 응답 센터와 Azure Security Center에서 감지 및 경고된 위협 간의 차이점은 무엇입니까?
Microsoft 보안 대응 센터(MSRC)는 Azure 네트워크 및 인프라의 선택 보안 모니터링을 수행하고 타사에서 위협 인텔리전스 및 남용 불만 사항을 받습니다. MSRC는 불법적인 또는 권한 없는 당사자가 고객 데이터에 액세스했거나 고객의 Azure 사용이 사용 제한에 대한 조건을 준수하지 않는 것을 인식하면 보안 사고 관리자는 고객에게 알립니다. 보안 연락처를 지정하지 않은 경우 대개 Azure Security Center에 지정된 보안 연락처 또는 Azure 구독 소유자에게 메일을 전송하는 방식으로 알림이 수행됩니다.

보안 센터는 지속적으로 고객의 Azure 환경을 모니터링하고 다양한 잠재적인 악의적 활동을 자동으로 검색하도록 분석을 적용하는 Azure 서비스입니다. 이러한 감지는 보안 센터 대시보드에서 보안 경고로 표시됩니다.