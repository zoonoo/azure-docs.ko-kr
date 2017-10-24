---
title: "Azure Security Center 보안 정책 소개 | Microsoft Docs"
description: "Azure Security Center 보안 정책 및 주요 기능에 대해 알아봅니다."
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: yurid
ms.openlocfilehash: f85f7077e5227818d062d114370e7344601a998e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="security-policies-overview"></a>보안 정책 개요
이 문서는 Security Center의 보안 정책에 대한 개요를 제공합니다.

## <a name="what-are-security-policies"></a>보안 정책이란?
보안 정책은 워크로드에서 원하는 구성을 정의하고 회사 또는 규정 보안 요구 사항을 준수하는 데 도움이 됩니다. Security Center에서 Azure 구독에 대한 정책을 정의할 수 있습니다. 이를 워크로드의 유형 또는 데이터의 민감도에 맞게 조정할 수 있습니다. 예를 들어 개인 식별 정보와 같이 규제된 데이터를 사용하는 응용 프로그램은 다른 작업보다 높은 수준의 보안이 필요할 수 있습니다. 

Security Center 정책에는 다음 구성 요소가 포함되어 있습니다.

- 데이터 컬렉션: 에이전트 프로비전 및 [데이터 컬렉션](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) 설정입니다.
- 보안 정책: Security Center에서 모니터링하고 권장하는 컨트롤을 결정하는 [Azure 정책](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)입니다.(Security Center에서 편집하거나 Azure Policy를 사용하여 새 정의를 작성하고 추가 정책을 정의하며 관리 그룹간에 정책을 할당합니다.)
- 전자 메일 알림: 보안 연락처 및 [전자 메일 알림](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) 설정입니다.
- 가격 책정 계층: 무료 또는 표준 [가격 책정 선택 영역](https://docs.microsoft.com/azure/security-center/security-center-pricing)입니다. 여기서 범위 내의 리소스에 사용할 수 있는 Security Center 기능을 결정합니다(구독, 리소스 그룹 및 작업 영역에 대해 지정할 수 있음). 


## <a name="who-can-edit-security-policies"></a>보안 정책은 누가 편집할 수 있나요?
Security Center는 Azure에서 사용자, 그룹 및 서비스에 할당할 수 있는 기본 제공 역할을 제공하는 RBAC(역할 기반 액세스 제어)를 사용합니다. 사용자가 Security Center를 열면 액세스한 리소스와 관련된 정보만 표시됩니다. 이는 구독 또는 리소스가 속한 리소스 그룹에 대한 소유자, 참가자 또는 읽기 권한자의 역할이 사용자에게 할당된다는 것을 의미합니다. 이러한 역할 외에도 두 개의 특정한 Security Center 역할이 있습니다.

- 보안 읽기 권한자: 이 역할에 속하는 사용자는 권장 사항, 경고, 정책 및 상태를 비롯한 Security Center에 대한 권한을 볼 수 있지만 변경할 수는 없습니다.
- 보안 관리자: 보안 읽기 권한자와 동일하지만 보안 정책을 업데이트하고 권장 사항 및 경고를 해제할 수 있습니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Security Center의 보안 정책에 대해 알아보았습니다. Azure 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 권장 사항 관리](security-center-recommendations.md) - 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure 보안 센터에서 보안 상태 모니터링](security-center-monitoring.md) - Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure 보안 센터를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) — 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
- [Azure Security Center 데이터 보안](security-center-data-security.md) - Security Center에서 데이터를 관리하고 보호하는 방법을 알아봅니다.
* [Azure 보안 센터 FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) - 최신 Azure 보안 뉴스 및 정보를 가져옵니다.


