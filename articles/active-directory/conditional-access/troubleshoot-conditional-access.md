---
title: 조건부 액세스 - Azure Active Directory의 로그인 문제 해결
description: 이 문서에서는 조건부 액세스 정책으로 인해 예기치 않은 결과가 발생할 때 수행할 작업을 설명합니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 03/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf1daca79f3ed20d9b7a89af20d74ff5f3148b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337442"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>조건부 액세스로 로그인 문제 해결

이 문서의 정보는 오류 메시지 및 Azure AD 로그인 로그인 로그를 사용하여 조건부 액세스와 관련된 예기치 않은 로그인 결과 문제를 해결하는 데 사용할 수 있습니다.

## <a name="conditional-access-sign-in-interrupt"></a>조건부 액세스 로그인 인터럽트

첫 번째 방법은 나타나는 오류 메시지를 검토하는 것입니다. 웹 브라우저를 사용할 때 로그인하는 문제의 경우 오류 페이지 자체에 자세한 정보가 있습니다. 이 정보만으로도 문제가 무엇이며 해결책을 제시할 수 있습니다.

![오류 로그인 - 호환 장치 필요](./media/troubleshoot-conditional-access/image1.png)

위의 오류에서 응용 프로그램은 회사의 모바일 장치 관리 정책을 충족하는 장치 또는 클라이언트 응용 프로그램에서만 액세스할 수 있음을 명시합니다. 이 경우 응용 프로그램 및 장치가 해당 정책을 충족하지 않습니다.

## <a name="azure-ad-sign-in-events"></a>Azure AD 로그인 이벤트

로그인 중단에 대한 자세한 정보를 얻는 두 번째 방법은 Azure AD 로그인 이벤트를 검토하여 적용된 조건부 액세스 정책 또는 정책과 그 이유를 확인하는 것입니다.

초기 오류 페이지에서 **자세한 내용을** 클릭하여 문제에 대한 자세한 내용을 확인할 수 있습니다. **자세한 내용을** 클릭하면 사용자가 본 특정 오류 이벤트에 대한 Azure AD 로그인 이벤트를 검색하거나 Microsoft에서 지원 인시던트를 열 때 유용한 문제 해결 정보가 표시됩니다.

![조건부 액세스에서 자세한 내용은 웹 브라우저 로그인을 중단했습니다.](./media/troubleshoot-conditional-access/image2.png)

어떤 조건부 액세스 정책 또는 정책이 적용되었으며 그 이유는 다음과 같은 이유입니다.

1. **Azure 포털에** 글로벌 관리자, 보안 관리자 또는 전역 판독기로 로그인합니다.
1. Azure **Active 디렉터리** > **로그인으로**검색합니다.
1. 로그인하여 검토할 이벤트를 찾습니다. 필터와 열을 추가하거나 제거하여 불필요한 정보를 필터링합니다.
   1. 범위를 좁히기 위해 필터를 추가합니다.
      1. 조사할 특정 이벤트가 있는 경우 **상관 관계 ID입니다.**
      1. 정책 실패 및 성공을 확인하기 위한 **조건부 액세스.** 필터의 범위를 확장하여 결과를 제한하는 실패만 표시합니다.
      1. **사용자 이름을** 사용하여 특정 사용자와 관련된 정보를 볼 수 있습니다.
      1. 문제의 시간 프레임으로 범위가 지정된 **날짜입니다.**

   ![로그인 로그에서 조건부 액세스 필터 선택](./media/troubleshoot-conditional-access/image3.png)

1. 사용자의 로그인 실패에 해당하는 로그인 이벤트가 발견되면 **조건부 액세스** 탭을 선택합니다. 조건부 액세스 탭에는 로그인 중단으로 인한 특정 정책 또는 정책이 표시됩니다.
   1. **문제 해결 및 지원** 탭의 정보는 규정 준수 요구 사항을 충족하지 않는 장치와 같이 로그인이 실패한 이유에 대한 명확한 이유를 제공할 수 있습니다.
   1. 자세한 조사를 위해 **정책 이름을**클릭하여 정책 구성을 드릴다운합니다. 정책 **이름을** 클릭하면 검토 및 편집을 위해 선택한 정책에 대한 정책 구성 사용자 인터페이스가 표시됩니다.
   1. 조건부 액세스 정책 평가에 사용된 **클라이언트 사용자** 및 **장치 세부 정보는** 기본 **정보,** **위치,** **장치 정보,** **인증 세부 정보**및 로그인 이벤트의 추가 **세부 정보** 탭에서도 사용할 수 있습니다.

   ![이벤트 조건부 액세스 탭에 로그인](./media/troubleshoot-conditional-access/image5.png)

이벤트의 정보가 로그인 결과를 이해하거나 원하는 결과를 얻기 위해 정책을 조정하기에 충분하지 않은 경우 지원 인시던트가 열릴 수 있습니다. 로그인 이벤트의 **문제 해결 및 지원** 탭으로 이동하여 새 지원 요청 **만들기를**선택합니다.

![로그인 이벤트의 트러블슈팅 및 지원 탭](./media/troubleshoot-conditional-access/image6.png)

인시던트를 제출할 때 인시던트 제출 세부 정보에서 로그인 이벤트에서 요청 ID와 시간 및 날짜를 제공합니다. 이 정보를 통해 Microsoft 지원팀은 우려되는 이벤트를 찾을 수 있습니다.

### <a name="conditional-access-error-codes"></a>조건부 액세스 오류 코드

| 로그인 오류 코드 | 오류 문자열 |
| --- | --- |
| 53000 | 장치 비준수 |
| 53001 | 장치 Not도메인가입 |
| 53002 | 응용 프로그램사용이안승인앱 |
| 53003 | 차단된조건액세스 |
| 53004 | 프루프차단듀토리스크 |

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 포털의 로그인 활동 보고서](../reports-monitoring/concept-sign-ins.md)
- [What If 도구를 사용하여 조건부 액세스 문제 해결](troubleshoot-conditional-access-what-if.md)
- [Azure Active 디렉터리에서 조건부 액세스에](best-practices.md) 대한 모범 사례
