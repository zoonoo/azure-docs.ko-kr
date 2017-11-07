---
title: "Azure Security Center에서 데이터 수집 | Microsoft Docs"
description: " Azure 보안 센터에서 데이터 수집을 활성화하는 방법을 알아봅니다. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 226fc82abf7aa24a0aa1bd3c21279158e1ce8e95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="data-collection-in-azure-security-center"></a>Azure Security Center에서 데이터 수집
Security Center는 Azure VM(Virtual Machines) 및 비 Azure 컴퓨터에서 데이터를 수집하여 보안 취약성과 위협을 모니터링합니다. Microsoft Monitoring Agent를 사용하여 데이터를 수집합니다. Microsoft Monitoring Agent는 컴퓨터에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석용으로 작업 영역에 데이터를 복사합니다. 이러한 데이터의 예: 운영 체제 유형 및 버전, 운영 체제 로그(Windows 이벤트 로그), 프로세스 실행, 컴퓨터 이름, IP 주소, 로그인된 사용자 및 테넌트 ID입니다. 또한 Microsoft Monitoring Agent는 작업 영역에 크래시 덤프 파일을 복사합니다.

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>Microsoft Monitoring Agent 자동 프로비저닝 사용     
자동 프로비저닝을 사용하도록 설정하면 Security Center는 지원되는 모든 Azure VM 및 새로 만든 Azure VM에 Microsoft Monitoring Agent를 프로비전합니다. 자동 프로비저닝은 사용하는 것이 좋으며, Security Center 표준 계층의 구독에서는 자동 프로비저닝을 반드시 사용해야 합니다.

> [!NOTE]
> 자동 프로비저닝을 사용하지 않도록 설정하면 리소스에 대한 보안 모니터링이 제한됩니다. 자세한 내용은 이 문서의 [자동 프로비저닝을 사용하지 않도록 설정](security-center-enable-data-collection.md#disable-automatic-provisioning)을 참조하세요. 자동 프로비저닝을 사용하지 않도록 설정하더라도 VM 디스크 스냅숏 및 아티팩트 수집은 사용하도록 설정됩니다.
>
>

Microsoft Monitoring Agent의 자동 프로비저닝을 사용하도록 설정하려면 다음을 수행합니다.
1. Security Center 주 메뉴 아래에서 **보안 정책**을 선택합니다.
2. 구독을 선택합니다.
3. **보안 정책** 아래에서 **데이터 수집**을 선택합니다.
4. **온보딩** 아래에서 **설정**을 선택하여 자동 프로비저닝을 사용하도록 설정합니다.
5. **저장**을 선택합니다.

![자동 프로비저닝 사용][1]

## <a name="default-workspace-configuration"></a>기본 작업 영역 구성
Security Center에서 수집된 데이터는 Log Analytics 작업 영역에 저장됩니다.  Security Center에서 작성된 작업 영역에 저장되어 있는 Azure VM이나 직접 만든 기존 작업 영역에서 데이터를 수집할 수 있습니다.

기존 Log Analytics 작업 영역을 사용하려면 다음을 수행합니다.
- 작업 영역이 선택한 Azure 구독과 연결되어 있어야 합니다.
- 작업 영역에 액세스하려면 읽기 이상의 권한이 필요합니다.

기존 Log Analytics 작업 영역을 선택하려면 다음을 수행합니다.

1. **보안 정책 - 데이터 수집** 아래에서 **다른 작업 영역 사용**을 선택합니다.

   ![기존 작업 영역 선택][2]

2. 풀다운 메뉴에서 수집된 데이터를 저장할 작업 영역을 선택합니다.

> [!NOTE]
> 풀다운 메뉴에는 액세스 권한이 있으며 Azure 구독에 포함된 작업 영역만 표시됩니다.
>
>

3. **저장**을 선택합니다.
4. **저장**을 선택하고 나면 모니터링되는 VM을 다시 구성할지를 묻는 메시지가 표시됩니다.

   - 새 작업 영역 설정을 새 VM에만 적용하려면 **아니요**를 선택합니다. 그러면 새 작업 영역 설정이 새 Agent 설치(Microsoft Monitoring Agent가 설치되어 있지 않은 새로 검색된 VM)에만 적용됩니다.
   - 새 작업 영역 설정을 모든 VM에 적용하려면 **예**를 선택합니다. 이 경우에는 Security Center에서 만든 작업 영역에 연결된 모든 VM이 새 대상 작업 영역에 다시 연결됩니다.

   > [!NOTE]
   > 예를 선택하는 경우 모든 VM이 새 대상 작업 영역에 다시 연결될 때까지는 Security Center에서 작성된 작업 영역을 삭제하면 안 됩니다. 작업 영역을 너무 빨리 삭제하면 이 작업이 실패합니다.
   >
   >

   - 작업을 취소하려면 **취소**를 선택합니다.

   ![기존 작업 영역 선택][3]

## <a name="data-collection-tier"></a>데이터 수집 계층
Security Center는 조사, 감사 및 위협 검색용 이벤트는 충분히 유지하면서 이벤트의 양을 줄일 수 있습니다. Agent가 수집하도록 할 4개 이벤트 집합 중에서 구독 및 작업 영역에 적합한 필터링 정책을 선택할 수 있습니다.

- **모든 이벤트** – 모든 이벤트를 수집하려는 고객용입니다. 이것이 기본값입니다.
- **일반** – 대다수 고객의 요구를 충족하는 이벤트 집합으로, 전체 감사 평가판을 확인할 수 있습니다.
- **최소** – 이벤트의 양을 최소화하려는 고객을 위한 더 작은 이벤트 집합입니다.
- **없음** – 보안 및 App Locker 로그에서 보안 이벤트를 수집하지 않도록 설정합니다. 이 옵션을 선택하는 고객의 보안 대시보드에는 Windows 방화벽 로그 및 맬웨어 방지/초기 계획/업데이트 등의 자동 관리 평가만 포함됩니다.

> [!NOTE]
> 이러한 집합은 일반적인 시나리오를 해결하도록 설계되었습니다. 특정 집합을 구현하기 전에 요구에 적합한 집합을 평가하세요.
>
>

Microsoft는 **일반** 및 **최소** 이벤트 집합에 포함할 이벤트를 결정하기 위해 다수의 고객과 협력하고 업계 표준을 활용하여 각 이벤트의 필터링 미적용 시 빈도와 해당 사용 방식을 파악했습니다. 그에 따라 이 프로세스에서는 다음 지침이 사용되었습니다.

- **최소** - 이 집합에는 보안 침해 성공을 나타낼 수 있는 이벤트와 중요 이벤트(양이 매우 적음)만 포함하세요. 예를 들어 로그인 성공/실패(이벤트 ID 4624, 4625)는 이 집합에 포함되지만 로그아웃은 포함되지 않습니다. 로그아웃의 경우 감사에서는 중요하지만 검색에서는 중요하지 않으며 양이 비교적 많기 때문입니다. 이 집합에 포함되는 데이터 볼륨 중 대부분은 로그인 이벤트 및 프로세스 만들기 이벤트(이벤트 ID 4688)입니다.
- **일반** - 이 집합에서는 전체 사용자 감사 내역을 제공하세요. 예를 들어 사용자 로그인 및 사용자 로그오프(이벤트 ID 4634)를 모두 이 집합에 포함합니다. Microsoft는 보안 그룹 변경, 주요 도메인 컨트롤러 Kerberos 작업, 그리고 업계의 조직에서 권장하는 기타 이벤트와 같은 감사 작업을 이 집합에 포함했습니다.

양이 매우 적은 이벤트는 일반 집합에 포함되었습니다. 모든 이벤트를 선택하는 대신 이러한 이벤트를 선택하는 주된 이유는 특정 이벤트를 필터링하여 제외하기 위해서가 아니라 이벤트의 양을 줄이기 위해서이기 때문입니다.

아래에는 각 집합별 전체 보안 및 App Locker 이벤트 ID가 나와 있습니다.

   ![이벤트 ID][4]

필터링 정책을 선택하려면 다음을 수행합니다.
1. **보안 정책 및 설정** 블레이드의 **보안 이벤트**에서 필터링 정책을 선택합니다.
2. **저장**을 선택합니다.

   ![필터링 정책 선택][5]

## <a name="disable-automatic-provisioning"></a>자동 프로비저닝을 사용하지 않도록 설정
보안 정책에서 자동 프로비저닝 설정을 해제하여 언제든지 리소스에서 자동 프로비저닝을 사용하지 않도록 설정할 수 있습니다. 시스템 업데이트, OS 취약성 및 끝점 보호에 대한 보안 경고와 권장 사항을 받으려면 자동 프로비저닝을 사용하는 것이 좋습니다.

> [!NOTE]
> 자동 프로비저닝을 해제해도 Microsoft Monitoring Agent가 프로비전된 Azure VM에서 Agent가 제거되지는 않습니다.
>
>

1. Security Center 주 메뉴로 돌아가서 보안 정책을 선택합니다.

   ![자동 프로비저닝을 사용하지 않도록 설정][6]

2. 자동 프로비저닝을 사용하지 않도록 설정할 구독을 선택합니다.
3. **보안 정책 - 데이터 수집** 블레이드의 **온보딩**에서 **해제**를 선택하여 자동 프로비저닝을 사용하지 않도록 설정합니다.
4. **저장**을 선택합니다.  

## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center에서 데이터 수집 및 자동 프로비저닝이 작동하는 방식에 대해 알아보았습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 권장 사항 관리](security-center-recommendations.md) -- 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure 보안 센터에서 보안 상태 모니터링](security-center-monitoring.md)--Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)--보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure 보안 센터를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) -- 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
- [Azure Security Center 데이터 보안](security-center-data-security.md) - Security Center에서 데이터를 관리하고 보호하는 방법을 알아봅니다.
* [Azure 보안 센터 FAQ](security-center-faq.md)--서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/)-- 최신 Azure 보안 뉴스 및 정보를 가져옵니다.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[4]: ./media/security-center-enable-data-collection/event-id.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-automatic-provisioning.png
