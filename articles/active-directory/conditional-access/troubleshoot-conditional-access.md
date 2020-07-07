---
title: 조건부 액세스로 로그인 문제 해결-Azure Active Directory
description: 이 문서에서는 조건부 액세스 정책으로 인해 예기치 않은 결과가 발생 하는 경우 수행할 작업을 설명 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9a2ab4b1ebc1c958be9dc4bd07a010f7fef8afc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82610521"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>조건부 액세스 관련 로그인 문제 해결

이 문서의 정보는 오류 메시지 및 Azure AD 로그인 로그를 사용 하 여 조건부 액세스와 관련 된 예기치 않은 로그인 결과의 문제를 해결 하는 데 사용할 수 있습니다.

## <a name="conditional-access-sign-in-interrupt"></a>조건부 액세스 로그인 인터럽트

첫 번째 방법은 표시 되는 오류 메시지를 검토 하는 것입니다. 웹 브라우저를 사용 하는 경우 로그인 하는 데 문제가 있는 경우 오류 페이지 자체에 자세한 정보가 포함 됩니다. 이 정보 만으로는 문제를 설명 하 고 해결 방법을 제안할 수 있습니다.

![로그인 오류 호환 장치 필요](./media/troubleshoot-conditional-access/image1.png)

위의 오류에서 메시지는 회사의 모바일 장치 관리 정책을 충족 하는 장치 또는 클라이언트 응용 프로그램 에서만 응용 프로그램에 액세스할 수 있음을 알려 주는 것입니다. 이 경우 응용 프로그램 및 장치는 해당 정책을 충족 하지 않습니다.

## <a name="azure-ad-sign-in-events"></a>Azure AD 로그인 이벤트

로그인 중단에 대 한 자세한 정보를 가져오는 두 번째 방법은 Azure AD 로그인 이벤트를 검토 하 여 적용 된 조건부 액세스 정책 또는 정책을 확인 하는 것입니다.

초기 오류 페이지에서 **자세한** 정보를 클릭 하 여 문제에 대 한 자세한 정보를 찾을 수 있습니다. **세부 정보** 를 클릭 하면 사용자가 확인 한 특정 오류 이벤트에 대 한 Azure AD 로그인 이벤트를 검색 하거나 Microsoft에서 지원 인시던트를 열 때 도움이 되는 문제 해결 정보가 표시 됩니다.

![조건부 액세스에 대 한 자세한 내용은 웹 브라우저 로그인이 중단 되었습니다.](./media/troubleshoot-conditional-access/image2.png)

적용 되는 조건부 액세스 정책 또는 정책을 확인 하 고 다음을 수행 하는 이유를 확인 합니다.

1. 전역 관리자, 보안 관리자 또는 전역 읽기 권한자로 **Azure Portal** 에 로그인 합니다.
1. **Azure Active Directory**  >  **로그인**으로 이동 합니다.
1. 검토할 로그인에 대 한 이벤트를 찾습니다. 필터 및 열을 추가 하거나 제거 하 여 불필요 한 정보를 필터링 합니다.
   1. 필터를 추가 하 여 범위 좁히기:
      1. 조사할 특정 이벤트가 있는 경우의 **상관 관계 ID** 입니다.
      1. 정책 실패 및 성공 여부를 확인 하려면 **조건부 액세스** 를 사용 합니다. 결과를 제한 하는 오류만 표시 하도록 필터 범위를 표시 합니다.
      1. 사용자 **이름** -특정 사용자와 관련 된 정보를 확인 합니다.
      1. 문제가 있는 시간 프레임으로 범위가 지정 된 **날짜** 입니다.

   ![로그인 로그에서 조건부 액세스 필터를 선택 합니다.](./media/troubleshoot-conditional-access/image3.png)

1. 사용자의 로그인 실패에 해당 하는 로그인 이벤트가 발견 되 면 **조건부 액세스** 탭을 선택 합니다. 조건부 액세스 탭에는 로그인 중단을 일으킨 특정 정책 또는 정책이 표시 됩니다.
   1. **문제 해결 및 지원** 탭의 정보는 규정 준수 요구 사항을 충족 하지 않는 장치 등의 로그인 실패 이유에 대 한 명확한 이유를 제공할 수 있습니다.
   1. 자세히 조사 하려면 **정책 이름을**클릭 하 여 정책 구성으로 드릴 다운 합니다. **정책 이름을** 클릭 하면 검토 및 편집을 위해 선택한 정책에 대 한 정책 구성 사용자 인터페이스가 표시 됩니다.
   1. 조건부 액세스 정책 평가에 사용 된 **클라이언트 사용자** 및 **장치 세부 정보** 는 **기본 정보**, **위치**, **장치 정보**, **인증 세부 정보**및 로그인 이벤트의 **추가 세부 정보** 탭 에서도 사용할 수 있습니다.
   1. 정책의 오른쪽에 있는 줄임표를 선택 하면 정책 세부 정보가 표시 됩니다. 이를 통해 관리자는 정책이 성공적으로 적용 된 이유에 대 한 추가 정보를 제공 합니다.

   ![로그인 이벤트 조건부 액세스 탭](./media/troubleshoot-conditional-access/image5.png)

   ![정책 세부 정보 (미리 보기)](./media/troubleshoot-conditional-access/policy-details.png)

이벤트의 정보가 로그인 결과를 이해 하기에 충분 하지 않거나 정책을 조정 하 여 원하는 결과를 얻을 수 없는 경우 지원 인시던트가 열립니다. 해당 로그인 이벤트의 **문제 해결 및 지원** 탭으로 이동 하 고 **새 지원 요청 만들기**를 선택 합니다.

![로그인 이벤트의 문제 해결 및 지원 탭](./media/troubleshoot-conditional-access/image6.png)

인시던트를 제출 하는 경우 인시던트 제출 세부 정보의 로그인 이벤트에서 요청 ID와 시간 및 날짜를 제공 합니다. 이 정보를 통해 Microsoft 지원에서 걱정 하는 이벤트를 찾을 수 있습니다.

### <a name="conditional-access-error-codes"></a>조건부 액세스 오류 코드

| 로그인 오류 코드 | 오류 문자열 |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 포털의 로그인 활동 보고서](../reports-monitoring/concept-sign-ins.md)
- [What If 도구를 사용하여 조건부 액세스 문제 해결](troubleshoot-conditional-access-what-if.md)
- [Azure Active Directory의 조건부 액세스](best-practices.md) 에 대 한 모범 사례
