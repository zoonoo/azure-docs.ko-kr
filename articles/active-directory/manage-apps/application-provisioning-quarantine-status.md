---
title: 격리의 응용 프로그램 프로 비전 상태 | Microsoft Docs
description: 자동 사용자 프로 비전을 위해 응용 프로그램을 구성한 경우 격리의 프로 비전 상태와이를 지우는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 933217b2ad86535d45f6674165ee162f263a8cd7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712166"
---
# <a name="application-provisioning-in-quarantine-status"></a>격리 상태의 응용 프로그램 프로 비전

Azure AD 프로 비전 서비스는 구성의 상태를 모니터링 하 고 비정상 앱을 "격리" 상태에 배치 합니다. 오류 (예: 잘못 된 관리자 자격 증명)로 인해 대상 시스템에 대해 수행 된 대부분의 호출이 일관 되 게 실패 하는 경우 프로 비전 작업은 격리 상태로 표시 됩니다.

격리 하는 동안 증분 주기 빈도는 하루에 한 번으로 점차 줄어듭니다. 모든 오류가 수정 되 고 다음 동기화 주기가 시작 된 후 프로 비전 작업은 격리에서 제거 됩니다. 프로 비전 작업이 4 주 넘게 격리 상태로 유지 되는 경우 프로 비전 작업은 사용 하지 않도록 설정 됩니다 (실행 중지).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>응용 프로그램이 격리 되어 있는지 여부를 확인 어떻게 할까요?

응용 프로그램이 격리 되어 있는지 여부를 확인 하는 방법에는 다음 세 가지가 있습니다.
  
- Azure Portal에서 **Azure Active Directory** > **Enterprise 응용 프로그램** * > &lt;* **프로 비전&gt;프로 비전** 을 탐색 하 고 맨 아래에 있는 진행률 표시줄로 스크롤합니다.  

  ![격리 상태를 보여 주는 프로 비전 상태 표시줄](media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Microsoft Graph request [Get synchronizationJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) 를 사용 하 여 프로 비전 작업의 상태를 프로그래밍 방식으로 가져옵니다.

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- 전자 메일을 확인 하세요. 응용 프로그램이 격리에 배치 되 면 일회성 알림 이메일이 전송 됩니다. 격리 이유가 변경 되 면 업데이트 된 전자 메일을 보내 새로운 격리 이유를 표시 합니다. 전자 메일이 표시 되지 않는 경우:

  - 응용 프로그램의 프로 비전 구성에서 유효한 **알림 전자 메일** 을 지정 했는지 확인 합니다.
  - 알림 전자 메일 받은 편지함에 스팸 필터링이 없는지 확인 합니다.
  - 전자 메일을 구독 취소 하지 않았는지 확인 합니다.

## <a name="why-is-my-application-in-quarantine"></a>응용 프로그램이 격리 된 이유는 무엇 인가요?

프로 비전 작업 상태를 가져오는 Microsoft Graph 요청은 다음과 같은 격리 이유를 표시 합니다.

- `EncounteredQuarantineException` 잘못 된 자격 증명이 제공 되었음을 나타냅니다. 프로 비전 서비스가 원본 시스템과 대상 시스템 간에 연결을 설정할 수 없습니다.

- `EncounteredEscrowProportionThreshold`는 프로비저닝이 에스크로 임계값을 초과 했음을 나타냅니다. 이 상태는 프로 비전 이벤트의 60% 이상이 실패 한 경우에 발생 합니다.

- `QuarantineOnDemand`는 응용 프로그램에 대 한 문제를 검색 하 고이를 수동으로 격리로 설정 했음을 의미 합니다.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>응용 프로그램을 격리에서 가져오지 어떻게 할까요??

먼저 응용 프로그램의 격리를 일으킨 문제를 해결 합니다.

- 응용 프로그램의 프로 비전 설정을 확인 하 여 [유효한 관리자 자격 증명을 입력](configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning)했는지 확인 합니다. Azure AD는 대상 응용 프로그램을 사용 하 여 트러스트를 설정할 수 있어야 합니다. 유효한 자격 증명을 입력 하 고 계정에 필요한 사용 권한이 있는지 확인 합니다.

- [프로 비전 로그](../reports-monitoring/concept-provisioning-logs.md) 를 검토 하 여 격리를 발생 시키는 오류를 자세히 조사 하 고 오류를 해결 합니다. **작업** 섹션에서 **Azure Active Directory** &gt; **Enterprise Apps** &gt; **프로 비전 로그 (미리 보기)** 로 이동 하 여 Azure Portal의 프로 비전 로그에 액세스 합니다.

문제를 해결 한 후 프로 비전 작업을 다시 시작 합니다. 특성 매핑 또는 범위 지정 필터와 같은 응용 프로그램의 프로 비전 설정에 대 한 특정 변경 사항은 자동으로 자동으로 프로 비전을 다시 시작 합니다. 응용 프로그램의 **프로 비전** 페이지에 있는 진행률 표시줄에는 프로 비전이 마지막으로 시작 된 시간을 나타냅니다. 프로 비전 작업을 수동으로 다시 시작 해야 하는 경우 다음 방법 중 하나를 사용 합니다.  

- Azure Portal를 사용 하 여 프로 비전 작업을 다시 시작 합니다. 응용 프로그램의 **프로 비전** 페이지 **설정**아래에서 **상태 지우기 및 동기화 다시 시작** 을 선택 하 고 **프로 비전 상태** 를 **켜기**로 설정 합니다. 이 작업은 프로 비전 서비스를 완전히 다시 시작 하며,이 작업은 다소 시간이 걸릴 수 있습니다. Escrows를 지우고 앱을 격리에서 제거 하 고 모든 워터 마크를 제거 하는 전체 초기 주기가 다시 실행 됩니다.

- Microsoft Graph를 사용 하 여 [프로 비전 작업을 다시 시작](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)합니다. 다시 시작 하는 작업을 완전히 제어할 수 있습니다. Escrows (격리 상태를 적용 하는 에스크로 카운터를 다시 시작 하려면)를 선택 취소 하거나 격리 (격리에서 응용 프로그램을 제거 하려면)를 지우거 나 워터 마크를 지울 수 있습니다. 다음 요청을 사용합니다.
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`