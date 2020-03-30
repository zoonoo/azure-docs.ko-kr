---
title: 검역소의 신청 프로비저닝 상태 | 마이크로 소프트 문서
description: 자동 사용자 프로비저닝을 위한 응용 프로그램을 구성한 경우 검역소의 프로비저닝 상태가 무엇을 의미하는지, 어떻게 지우는지 알아봅니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563c049bf3d1606e87db54e3b003dac987594610
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154630"
---
# <a name="application-provisioning-in-quarantine-status"></a>격리 상태의 응용 프로그램 프로비저닝

Azure AD 프로비저닝 서비스는 구성의 상태를 모니터링하고 비정상 앱을 "격리" 상태에 배치합니다. 오류(예: 잘못된 관리자 자격 증명)로 인해 대상 시스템에 대해 수행된 호출의 대부분 또는 전부가 일관되게 실패하면 프로비저닝 작업이 격리된 것으로 표시됩니다.

검역하는 동안 증분 주기의 빈도는 점차 하루에 한 번으로 줄어듭니다. 모든 오류가 수정되고 다음 동기화 주기가 시작된 후 프로비저닝 작업이 격리에서 제거됩니다. 프로비저닝 작업이 4주 이상 격리된 상태로 유지되면 프로비저닝 작업이 비활성화됩니다(실행 중지).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>신청서가 검역 중인지 어떻게 알 수 있습니까?

응용 프로그램이 격리되어 있는지 여부를 확인하는 방법에는 세 가지가 있습니다.
  
- Azure 포털에서 Azure **Active Directory** > **Enterprise 응용 프로그램** > &lt;*이름*&gt; > **프로비저닝으로** 이동한 다음 아래쪽의 진행률 표시줄로 스크롤합니다.  

  ![격리 상태를 표시하는 프로비저닝 상태 표시줄](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Azure 포털에서 Azure **Active Directory** > **감사 로그** > **활동: 격리** 및 격리 기록 검토에 대한 필터로 이동합니다. 위에서 설명한 진행률 표시줄의 보기는 프로비저닝이 현재 격리되어 있는지 여부를 보여 주지만 감사 로그를 사용하면 응용 프로그램의 격리 기록을 볼 수 있습니다. 

- Microsoft 그래프 요청을 사용하여 [synchronizationJob을](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) 프로그래밍 방식으로 프로비저닝 작업의 상태를 가져옵니다.

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- 이메일을 확인합니다. 응용 프로그램이 격리된 경우 일회성 알림 전자 메일이 전송됩니다. 격리 사유가 변경되면 격리에 대한 새로운 이유를 보여주는 업데이트된 전자 메일이 전송됩니다. 이메일이 표시되지 않는 경우:

  - 응용 프로그램의 프로비저닝 구성에서 유효한 **알림 전자 메일을** 지정했는지 확인합니다.
  - 알림 이메일 받은 편지함에 스팸 필터링이 없는지 확인합니다.
  - 이메일 구독을 취소하지 않았는지 확인합니다.

## <a name="why-is-my-application-in-quarantine"></a>검역 신청서가 검역되는 이유는 무엇입니까?

프로비저닝 작업의 상태를 얻기 위한 Microsoft 그래프 요청에는 격리에 대한 다음과 같은 이유가 표시됩니다.

- `EncounteredQuarantineException`은 잘못된 자격 증명이 제공되었음을 나타냅니다. 프로비저닝 서비스는 원본 시스템과 대상 시스템 간의 연결을 설정할 수 없습니다.

- `EncounteredEscrowProportionThreshold`는 프로비저닝이 에스크로 임계값을 초과했음을 나타냅니다. 이 조건은 프로비저닝 이벤트의 60% 이상이 실패했을 때 발생합니다.

- `QuarantineOnDemand`즉, 응용 프로그램에 문제가 발견되어 수동으로 격리되도록 설정했습니다.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>신청서를 격리에서 제외하려면 어떻게 해야 합니까?

먼저 응용 프로그램을 격리된 상태로 만드는 문제를 해결합니다.

- 응용 프로그램의 프로비저닝 설정을 확인하여 [유효한 관리자 자격 증명을 입력했는지 확인합니다.](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning) Azure AD는 대상 응용 프로그램과 함께 트러스트를 설정할 수 있어야 합니다. 유효한 자격 증명을 입력하고 계정에 필요한 권한이 있는지 확인합니다.

- [프로비저닝 로그를](../reports-monitoring/concept-provisioning-logs.md) 검토하여 격리의 원인이 되는 오류를 더 자세히 조사하고 오류를 해결합니다. **활동** 섹션의 **Azure Active Directory** &gt; **엔터프라이즈 앱** &gt; **프로비저닝 로그(미리 보기)로** 이동하여 Azure 포털의 프로비저닝 로그에 액세스합니다.

문제를 해결한 후 프로비저닝 작업을 다시 시작합니다. 특성 매핑 또는 범위 지정 필터와 같은 응용 프로그램의 프로비저닝 설정에 대한 특정 변경 사항은 자동으로 프로비저닝을 다시 시작합니다. 응용 프로그램의 **프로비저닝** 페이지의 진행률 표시줄은 프로비저닝이 마지막으로 시작된 시기를 나타냅니다. 프로비저닝 작업을 수동으로 다시 시작해야 하는 경우 다음 방법 중 하나를 사용합니다.  

- Azure 포털을 사용하여 프로비저닝 작업을 다시 시작합니다. **설정에서**응용 프로그램의 **프로비저닝** 페이지에서 **상태 지우기를 선택하고 동기화를 다시 시작하고** **프로비저닝 상태를** 설정 **합니다.** 이 작업은 프로비저닝 서비스를 완전히 다시 시작하므로 시간이 걸릴 수 있습니다. 전체 초기 주기가 다시 실행되어 에스크로를 지우고 격리에서 앱을 제거하고 워터 마크를 지웁니다.

- Microsoft 그래프를 사용하여 [프로비저닝 작업을 다시 시작합니다.](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) 다시 시작하는 내용을 완전히 제어할 수 있습니다. 에스크로 지우기(격리 상태로 발생하는 에스크로 카운터를 다시 시작하거나), 검역 취소(격리에서 응용 프로그램을 제거하려면) 또는 투명 워터마크를 취소할 수 있습니다. 다음 요청을 사용합니다.
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`
