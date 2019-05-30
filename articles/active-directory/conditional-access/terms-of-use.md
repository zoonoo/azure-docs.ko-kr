---
title: Azure Active Directory 사용 약관 | Microsoft Docs
description: Azure Active Directory 액세스 하기 위해 직원 또는 게스트가에 정보를 제공 하는 사용 약관을 사용 하 여 시작 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1abae0a454e17e8f633f68bc5853bfb4a4b24d14
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383170"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory 사용 약관

Azure AD 사용 약관에는 조직에서는 최종 사용자에 게 정보를 제공 하는 데 사용할 수 있는 간단한 방법을 제공 합니다. 이 프레젠테이션은 사용자가 법률 또는 규정 준수 요구 사항에 대한 관련 고지 사항을 볼 수 있게 해줍니다. 이 문서에는 사용 약관을 사용 하 여 시작 하는 방법을 설명 합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>개요 비디오

다음 비디오는 사용 약관의 간략 한 개요를 제공합니다.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

추가 비디오를 보려면 다음을 참조하세요.
- [Azure Active Directory의 사용 약관을 배포 하는 방법](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Azure Active Directory의 사용 약관을 출시 하는 방법](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>사용 약관을 사용 하 여 어떻게 해야 합니까?

Azure AD 사용 약관에는 다음 기능이 있습니다.

- 직원 또는 게스트가 액세스 하기 전에 사용 약관에 동의 해야 합니다.
- 직원 또는 게스트가 액세스 하기 전에 모든 장치에서 사용 약관에 동의 해야 합니다.
- 직원 또는 게스트가 되풀이 일정에 따라 사용 약관을 수락 하도록 해야 합니다.
- 직원 또는 게스트가 Azure Multi-factor Authentication (MFA)에서 보안 정보를 등록 하기 전에 사용 약관에 동의 해야 합니다.
- Azure AD 셀프 서비스 암호 재설정 (SSPR)에 보안 정보를 등록 하기 전에 사용 약관을 수락 하는 직원이 필요 합니다.
- 조직에서 일반 모든 사용자에 대 한 사용 약관을 제공 합니다.
- 특정 사용자 특성 (예:를 기반으로 하는 사용 약관 표시 [동적 그룹](../users-groups-roles/groups-dynamic-membership.md)을 사용하여 의사와 간호사, 국내 직원과 해외 직원에게 맞는 사용 약관을 각각 제공).
- Salesforce와 같은 높은 비즈니스 영향 응용 프로그램에 액세스 하는 경우 특정 사용 약관을 제공 합니다.
- 다른 언어로 사용 약관을 제공 합니다.
- 사용 약관을 수락 하지 않은 사용자 목록입니다.
- 개인 정보 보호 규정을 준수하도록 지원
- 사용 하 여 작업에 대 한 조건의 준수 및 감사 로그를 표시 합니다.
- 사용 약관을 만들고 [Microsoft Graph Api](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (현재 미리 보기로 제공).

## <a name="prerequisites"></a>필수 조건

를 사용 하 여 및 Azure AD 사용 약관을 구성 하려면 다음이 있어야 합니다.

- Azure AD Premium P1, P2, EMS E3 또는 EMS E5 구독.
   - 이러한 구독 중 하나가 없는 경우 [Azure AD Premium을 받거나](../fundamentals/active-directory-get-started-premium.md) [Azure AD Premium 평가판을 사용](https://azure.microsoft.com/trial/get-started-active-directory/)할 수 있습니다.
- 구성하려는 디렉터리에 대한 다음 관리자 계정 중 하나:
   - 전역 관리자
   - 보안 관리자
   - 조건부 액세스 관리자

## <a name="terms-of-use-document"></a>사용 약관 문서

Azure AD 사용 약관 PDF 형식으로 사용 하 여 콘텐츠를 제공 합니다. PDF 파일은 기존 계약서와 같이 사용자가 로그인하는 동안 최종 사용자 계약을 수집할 수 있는 모든 콘텐츠가 될 수 있습니다. 모바일 디바이스에서 사용자를 지원하기 위한 PDF의 권장 글꼴 크기는 24pt입니다.

## <a name="add-terms-of-use"></a>사용 약관 추가

문서 사용 약관을 완성 했으면 다음 절차를 사용 하 여 추가 합니다.

1. Azure에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. [https://aka.ms/catou](https://aka.ms/catou)에서 **사용 약관**으로 이동합니다.

   ![사용 약관 블레이드](./media/terms-of-use/tou-blade.png)

1. **새 사용 약관**을 클릭합니다.

   ![TOU 추가](./media/terms-of-use/new-tou.png)

1. 에 **이름을** 상자에 Azure portal에서 사용 되는 사용 약관에 대 한 이름을 입력 합니다.
1. **표시 이름** 상자에 사용자가 로그인할 때 표시되는 제목을 입력합니다.
1. 에 대 한 **사용 약관 문서**에 완성 된 사용 약관 PDF 찾아 선택 합니다.
1. 문서 사용 약관에 대 한 언어를 선택 합니다. 언어 옵션을 사용하면 사용 약관을 다양한 언어로 여러 개 업로드할 수 있습니다. 최종 사용자에게 표시되는 사용 약관의 버전은 브라우저 기본 설정에 기반합니다.
1. 동의 하기 전에 사용 약관을 확인 하려면 최종 사용자가 되도록 설정 **사용자가 사용 약관을 확장 하도록** 하 **에서**합니다.
1. 설정에서 액세스 하는 모든 장치에서 사용 약관을 수락 하도록 최종 사용자를 요구 하려면 **사용자가 모든 장치에서 동의 하도록 요구** 하 **에서**합니다. 자세한 내용은 [장치별 약관](#per-device-terms-of-use)합니다.
1. 사용 약관 동의 일정에 따라 만료 되도록 하려는 경우 설정할 **동의 만료** 하 **에서**합니다. 켜기로 설정되면 두 개의 추가 일정 설정이 표시됩니다.

   ![동의 만료](./media/terms-of-use/expire-consents.png)

1. 사용 합니다 **터 만료** 및 **빈도** 만료를 사용 하 여 약관에 대 한 일정을 지정 하는 설정입니다. 다음 표는 몇 가지 예제 설정의 결과를 보여 줍니다.

   | 만료 시작 날짜 | 빈도 | 결과 |
   | --- | --- | --- |
   | 오늘 날짜  | 매월 | 오늘부터 사용자 사용 약관에 동의 하며 매월 다시 수락 합니다. |
   | 미래 날짜  | 매월 | 오늘부터 사용자의 사용 약관을 동의 해야 합니다. 미래 날짜가 발생하면 동의가 만료되고 이후 사용자는 매월 다시 동의해야 합니다.  |

   예를 들어 만료 시작 날짜를 **1월 1일**로 설정하고 빈도를 **매월**로 설정하는 경우 사용자 두 명에 대한 만료는 다음과 같은 방식으로 발생합니다.

   | 사용자 | 첫 번째 동의 날짜 | 첫 번째 만료 날짜 | 두 번째 만료 날짜 | 세 번째 만료 날짜 |
   | --- | --- | --- | --- | --- |
   | Alice | 1월 1일 | 2월 1일 | 3월 1일 | 4월 1일 |
   | Bob | 1월 15일 | 2월 1일 | 3월 1일 | 4월 1일 |

1. 사용 하 여는 **다시 승인 필요 (일) 까지의 기간** 설정을 사용자의 사용 약관을 다시 수락 해야 하는 전 까지의 일 수를 지정 합니다. 이를 통해 사용자는 자신의 일정을 따를 수 있습니다. 예를 들어 기간을 **30**일로 설정하면 두 사용자에 대한 만료가 다음과 같은 방식으로 발생합니다.

   | 사용자 | 첫 번째 동의 날짜 | 첫 번째 만료 날짜 | 두 번째 만료 날짜 | 세 번째 만료 날짜 |
   | --- | --- | --- | --- | --- |
   | Alice | 1월 1일 | 1월 31일 | 3월 2일 | 4월 1일 |
   | Bob | 1월 15일 | 2월 14일 | 3월 16일 | 4월 15일 |

   **동의 만료** 및 **다시 동의가 필요하기 전 기간(일)** 설정을 함께 사용할 수 있지만, 일반적으로 두 설정 중 하나만 사용합니다.

1. 아래 **조건부 액세스**를 사용 합니다 **조건부 액세스 정책 템플릿으로 적용** 사용 약관에 적용할 템플릿을 선택 하는 목록.

   ![조건부 액세스 템플릿](./media/terms-of-use/conditional-access-templates.png)

   | Template | 설명 |
   | --- | --- |
   | **모든 게스트에 대한 클라우드 앱 액세스** | 모든 게스트 및 모든 클라우드 앱에 대한 조건부 액세스 정책이 생성됩니다. 이 정책은 Azure Portal에 영향을 줍니다. 이 항목이 생성되면 로그아웃했다 로그인해야 할 수 있습니다. |
   | **모든 사용자에 대한 클라우드 앱 액세스** | 모든 사용자 및 모든 클라우드 앱에 대한 조건부 액세스 정책이 생성됩니다. 이 정책은 Azure Portal에 영향을 줍니다. 이 항목이 생성되면 로그아웃했다 로그인해야 할 수 있습니다. |
   | **사용자 지정 정책** | 사용자, 그룹 및 사용이이 조건을 적용할 수 있는 앱을 선택 합니다. |
   | **나중에 조건부 액세스 정책 만들기** | 이 사용 약관은 조건부 액세스 정책을 만들 때 권한 부여 컨트롤 목록에 표시됩니다. |

   >[!IMPORTANT]
   >조건부 액세스 정책 제어(사용 조건 포함)는 서비스 계정에 적용을 지원하지 않습니다. 우리는 조건부 액세스 정책에서 모든 서비스 계정을 배제하도록 추천합니다.

    사용자 지정 조건부 액세스 정책을 세분화 된 특정 클라우드 응용 프로그램 또는 사용자 그룹의 사용 약관을 사용 하도록 설정 합니다. 자세한 내용은 [빠른 시작: 클라우드 앱에 액세스하기 전에 사용 약관에 동의하도록 요구](require-tou.md)를 참조하세요.

1. **만들기**를 클릭합니다.

   사용자 지정 조건부 액세스 템플릿을 선택한 경우 사용자 지정 조건부 액세스 정책을 만들 수 있는 새 화면이 나타납니다.

   ![사용자 지정 정책](./media/terms-of-use/custom-policy.png)

   이제 새 사용 약관 표시 됩니다.

   ![TOU 추가](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>사용 약관에 동의/거부한 사람에 대한 보고서 보기

[사용 약관] 블레이드에는 사용 약관에 동의한 사용자 및 거부한 사용자 수가 표시됩니다. 이러한 개수와는 수락/거부 된 사용 약관의 수명 동안 저장 됩니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.

   ![사용 약관 블레이드](./media/terms-of-use/view-tou.png)

1. 사용 약관에 대 한 아래의 숫자를 클릭 **수락 됨** 또는 **거부** 사용자에 대 한 현재 상태를 볼 수 있습니다.

   ![사용 약관 동의](./media/terms-of-use/accepted-tou.png)

1. 개별 사용자의 기록을 보려면 줄임표( **...** )를 클릭한 후 **기록 보기**를 클릭합니다.

   ![기록 보기 메뉴](./media/terms-of-use/view-history-menu.png)

   [기록 보기] 창에서 모든 승인, 거부 및 만료 기록을 확인합니다.

   ![기록 보기 창](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Azure AD 감사 로그 보기

추가 작업을 확인 하려는 경우 Azure AD 사용 약관 감사 로그를 포함 합니다. 각 사용자 동의는 **30일** 동안 저장된 감사 로그에서 이벤트를 트리거합니다. 포털에서 이러한 로그를 살펴보거나 .csv 파일로 다운로드할 수 있습니다.

Azure AD 감사 로그를 시작하려면 다음 절차를 따릅니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.
1. 사용 약관을 선택 합니다.
1. **감사 로그 보기**를 클릭합니다.

   ![사용 약관 블레이드](./media/terms-of-use/audit-tou.png)

1. Azure AD 감사 로그 화면에서 제공된 목록을 사용하여 특정 감사 로그 정보를 대상으로 하도록 정보를 필터링할 수 있습니다.

   **다운로드**를 클릭하여 로컬에서 사용하도록 정보를 .csv 파일로 다운로드할 수도 있습니다.

   ![감사 로그](./media/terms-of-use/audit-logs-tou.png)

   로그를 클릭하면 추가 활동 세부 정보가 포함된 창이 나타납니다.

   ![활동 세부 정보](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>사용자가 다음과 같이 어떤 사용 약관이 표시 됩니다.

사용 약관을 만들고 적용 범위에 있는 사용자를 로그인 하는 동안 다음 화면이 표시 됩니다.

![사용자 웹 로그인](./media/terms-of-use/user-tou.png)

사용자가 수의 사용 약관을 보고 필요한 경우 확대 / 축소 하려면 단추를 사용 합니다.

![확대/축소 단추를 사용 하 여 사용 약관 보기](./media/terms-of-use/zoom-buttons.png)

다음 화면에서는 모바일 장치에서 사용 약관 표시 되는 모양을 보여 줍니다.

![사용자 모바일 로그인](./media/terms-of-use/mobile-tou.png)

사용자가 약관에 동의 사용 하 여 한 번만 해야 하 고 표시 되지 않습니다 사용 약관 다시 후속 로그인 시.

### <a name="how-users-can-review-their-terms-of-use"></a>사용자 사용 약관을 검토할 수 있습니다 하는 방법

사용자 검토를 다음 절차를 사용 하 여 자신이 동의한 사용 약관을 참조 하세요.

1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에 로그인합니다.
1. 오른쪽 위 모서리에서 자신의 이름을 클릭하고 **프로필**을 선택합니다.

   ![프로필](./media/terms-of-use/tou14.png)

1. 프로필 페이지에서 **사용 약관 검토**를 클릭합니다.

   ![프로필 - 사용 약관 검토](./media/terms-of-use/tou13a.png)

1. 여기에서 사용자가 동의한 사용 약관을 검토할 수 있습니다.

## <a name="edit-terms-of-use-details"></a>사용 약관 세부 정보 편집

사용 약관에 대 한 세부 정보를 편집할 수 있지만 기존 문서를 수정할 수 없습니다. 다음 절차에서는 세부 정보를 편집하는 방법에 대해 설명합니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.
1. 편집할 사용 약관을 선택 합니다.
1. **사용 약관 편집**을 클릭합니다.
1. [사용 약관 편집] 창에서 이름을 변경하거나, 이름을 표시하거나, 사용자가 값을 확장하도록 요구합니다.

   설정이 다른 PDF 문서 등을 변경 하려는 경우 사용자가 동의 만료, 모든 장치에 동의 하도록 요구 기간 새 사용 약관 전에 reacceptance, 또는 조건부 액세스 정책을 만들어야 합니다.

   ![사용 약관 편집](./media/terms-of-use/edit-tou.png)

1. **저장**을 클릭하여 변경 내용을 저장합니다.

   변경 내용이 저장된 후 사용자가 이러한 편집 내용에 다시 동의할 필요가 없습니다.

## <a name="add-a-terms-of-use-language"></a>사용 하 여 언어의 사용 약관 추가

다음 절차에 사용 하 여 언어의 사용 약관을 추가 하는 방법을 설명 합니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.
1. 편집할 사용 약관을 선택 합니다.
1. 세부 정보 창에서 **언어** 탭을 클릭합니다.

   ![TOU 추가](./media/terms-of-use/languages-tou.png)

1. **언어 추가**를 클릭합니다.
1. [사용 약관 언어 추가] 창에서 지역화된 PDF를 업로드하고 언어를 선택합니다.

   ![TOU 추가](./media/terms-of-use/language-add-tou.png)

1. **추가**를 클릭하여 언어를 추가합니다.

## <a name="per-device-terms-of-use"></a>장치별 사용 약관

합니다 **사용자가 모든 장치에서 동의 하도록 요구** 설정을 사용 하 여 최종 사용자가에서 액세스 하는 모든 장치에서 사용 약관을 동의 해야 합니다. 최종 사용자는 Azure AD에서 해당 디바이스에 연결해야 합니다. 장치에 가입 하는 경우 각 장치에서 사용 약관을 적용 하는 장치 ID 사용 됩니다.

지원되는 플랫폼 및 소프트웨어 목록은 다음과 같습니다.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | 윈도우 10 | 기타 |
> | --- | --- | --- | --- | --- |
> | **네이티브 앱** | 예 | 예 | 예 |  |
> | **Microsoft Edge** | 예 | 예 | 예 |  |
> | **Internet Explorer** | 예 | 예 | 예 |  |
> | **Chrome(확장 포함)** | 예 | 예 | 예 |  |

장치별 사용 약관에 다음과 같은 제약 조건이 있습니다.

- 디바이스는 하나의 테넌트에만 연결될 수 있습니다.
- 사용자는 해당 디바이스에 연결할 권한이 있어야 합니다.
- Intune 등록 앱이 지원되지 않습니다.
- Azure AD B2B 사용자가 지원 되지 않습니다.

사용자의 디바이스가 연결되지 않으면 해당 디바이스에 연결해야 한다는 메시지가 표시됩니다. 해당 환경은 플랫폼 및 소프트웨어에 따라 다릅니다.

### <a name="join-a-windows-10-device"></a>Windows 10 디바이스 연결

사용자가 Windows 10 및 Microsoft Edge를 사용하는 경우 [해당 디바이스에 연결](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device)하기 위해 다음과 같은 메시지가 표시됩니다.

![Windows 10 및 Microsoft Edge - 디바이스 연결 프롬프트](./media/terms-of-use/per-device-win10-edge.png)

Chrome을 사용하는 경우 [Windows 10 계정 확장](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)을 설치할지 묻는 메시지가 표시됩니다.

### <a name="browsers"></a>브라우저

사용자가 지원되지 않는 브라우저를 사용하는 경우에는 다른 브라우저를 사용하도록 요청하는 메시지가 표시됩니다.

![지원되지 않는 브라우저](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>사용 약관 삭제

다음 절차를 사용 하 여 사용의 이전 용어를 삭제할 수 있습니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.
1. 제거할 사용 약관을 선택합니다.
1. **사용 약관 삭제**를 클릭합니다.
1. 계속 진행할 것인지 묻는 메시지가 표시되면 **예**를 클릭합니다.

   ![사용 약관 삭제](./media/terms-of-use/delete-tou.png)

   사용 약관을 더 이상 표시 됩니다.

## <a name="deleted-users-and-active-terms-of-use"></a>삭제 된 사용자 및 현재 사용 약관

기본적으로 삭제된 사용자는 30일 동안 Azure AD에 삭제된 상태로 남아 있지만, 이 기간 동안 관리자는 필요한 경우 삭제된 사용자를 복원할 수 있습니다. 30일 후 해당 사용자가 영구적으로 삭제됩니다. 또한 Azure Active Directory 포털을 사용하여 전역 관리자는 해당 기간이 만료되기 전에 명시적으로 [최근에 삭제된 사용자를 영구적으로 삭제](../fundamentals/active-directory-users-restore.md)할 수 있습니다. 하나는 사용자가 영구적으로 삭제 된, 해당 사용자에 대 한 후속 데이터 active 사용 약관에서 제거 됩니다. 삭제된 사용자에 대한 감사 정보는 감사 로그에 남아 있습니다.

## <a name="policy-changes"></a>정책 변경 내용

조건부 액세스 정책은 즉시 적용됩니다. 이 경우 관리자에게 "sad 클라우드" 또는 "Azure AD 토큰 문제"가 표시되기 시작합니다. 관리자는 새 정책을 충족하기 위해 로그아웃했다가 다시 로그인해야 합니다.

> [!IMPORTANT]
> 범위에 있는 사용자는 다음과 같은 경우 새 정책을 충족하기 위해 로그아웃 및 로그인해야 합니다.
>
> - 사용 약관에 조건부 액세스 정책이 설정된 경우
> - 또는 두 번째 사용 약관이 작성된 경우

## <a name="b2b-guests-preview"></a>B2B 게스트(미리 보기)

대부분의 조직 조직의 사용 및 개인정보 처리 방침 약관에 동의 하 여 내부 직원에 대 한 프로세스에 있습니다. 그러나 SharePoint 또는 Teams를 통해 추가될 경우 Azure AD B2B 게스트에게 동일한 동의를 어떻게 적용할 수 있나요? 조건부 액세스 및 사용 약관을 사용 하 여 B2B 게스트 사용자를 위한 직접 정책을 적용할 수 있습니다. 초대 상환 흐름 중 사용자의 사용 약관을 사용 하 여 표시 됩니다. 이 지원은 현재 미리 보기로 제공되고 있습니다.

사용 약관은 사용자에게 Azure AD의 게스트 계정이 있는 경우에만 표시됩니다. SharePoint Online 현재에 [임시 외부 공유 받은 사람 환경](/sharepoint/what-s-new-in-sharing-in-targeted-release) 문서 또는 사용자에 게 게스트 계정이 필요 하지 않은 폴더를 공유 합니다. 이 경우 사용 약관 표시 되지 않습니다.

![모든 게스트 사용자](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>클라우드 앱 지원(미리 보기)

사용 약관은 Azure Information Protection 및 Microsoft Intune과 같은 다른 클라우드 앱에 사용할 수 있습니다. 이 지원은 현재 미리 보기로 제공되고 있습니다.

### <a name="azure-information-protection"></a>Azure Information Protection

Azure Information Protection 앱에 대 한 조건부 액세스 정책을 구성할 수 있으며 사용자가 보호 된 문서에 액세스 하는 경우 사용 약관 필요할 수 있습니다. 이렇게 하면 사용자가 처음으로 보호 된 문서를 액세스 하 하기 전에 사용 약관 트리거됩니다.

![Azure Information Protection 클라우드 앱](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune 등록

Microsoft Intune 등록 응용 프로그램에 대 한 조건부 액세스 정책을 구성 하 고 Intune에서 장치를 등록 하기 전에 사용 약관을 필요한 수 있습니다. 자세한 내용은 [조직에 적합한 약관 솔루션 선택 블로그 게시물](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409) 읽기를 참조하세요.

![Microsoft Intune 클라우드 앱](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Intune 등록 응용 프로그램에 대 한 지원 되지 않습니다 [장치별 약관](#per-device-terms-of-use)합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

**Q: /는 사용자가 사용 약관을 수락 하는 경우 참조 하는 방법**<br />
A: 사용 약관 블레이드에서 **동의** 아래에 있는 숫자를 클릭합니다. Azure AD 감사 로그에서 동의 작업을 보거나 검색할 수도 있습니다. 자세한 내용은 사용 약관에 동의/거부한 사람에 대한 보고서 보기 및 [Azure AD 감사 로그 보기](#view-azure-ad-audit-logs)를 참조하세요.

**Q: 정보가 얼마나 오래 저장되나요?**<br />
A: 사용 하 여 보고서 및 사용 약관의 수명 동안 저장 됩니다 수락/거부 하는 사용자 수입니다. Azure AD 감사 로그는 30일 동안 저장됩니다.

**Q: 감사 로그 및 Azure AD 보고서 사용 약관에 동의 다른 숫자를 표시 하는 이유는 무엇 인가요?**<br />
A: 사용 하 여 보고서의 조건에는 해당 Azure AD 감사 로그는 30 일 동안 저장 됩니다 하는 동안 사용 약관의 수명 동안 저장 됩니다. 또한 사용 하 여 보고서의 조건에만 사용자가 현재 동의 상태를 표시 합니다. 예를 들어 사용자가 거부 하 고 다음가 허용을 사용 하 여 보고서의 조건에는 해당 사용자의 표시 수락 합니다. 기록을 확인해야 하는 경우, Azure AD 감사 로그를 사용할 수 있습니다.

**Q: 사용 약관에 대 한 세부 정보를 편집할 경우 해당 사용자가 다시 동의 필요 합니까?**<br />
A: 아니요, 관리자가 사용 약관에 대 한 세부 정보를 편집 하는 경우 (이름, 표시 이름, 확장, 사용자 또는 언어 추가) 새 항 여 사용자가 필요 하지 않습니다.

**Q: 기존 사용 약관 문서를 업데이트할 수 있습니까?**<br />
A: 현재는 기존 사용 약관 문서를 업데이트할 수 없습니다. 문서 사용 약관을 변경 하려면 사용 하 여 인스턴스의 새 사용 약관을 만들려고 해야 합니다.

**Q: 하이퍼링크는 사용 약관 PDF 문서에 있는 경우 최종 사용자가 할 수 클릭?**<br />
A: PDF는 기본적으로 JPEG로 렌더링되므로 하이퍼링크를 클릭할 수 없습니다. 사용자는 **보는 데 문제가 있나요? 여기를 클릭하세요.** 를 선택하여 하이퍼링크가 지원되는 네이티브 PDF를 렌더링할 수 있습니다.

**Q: 사용 약관 여러 언어를 지원할 수 있나요?**<br />
A: 예. 현재 단일에 대 한 관리자로 구성할 수 있습니다 108 다른 언어는 사용 약관입니다. 관리자는 여러 PDF 문서를 업로드하고 해당 언어를 사용하는 문서에 태그를 지정할 수 있습니다(최대 108개). 최종 사용자가 로그인할 때 브라우저 언어 기본 설정이 확인되고 일치하는 문서가 표시됩니다. 일치 항목이 없으면 업로드된 첫 번째 문서인 기본 문서가 표시됩니다.

**Q: 트리거 사용 약관의 경우**<br />
A: 사용 약관은 로그인 환경 중 트리거됩니다.

**Q: 응용 프로그램 I를 사용 하 여 약관을 대상 수 있습니다.?**<br />
A: 최신 인증을 사용하는 엔터프라이즈 애플리케이션에 조건부 액세스 정책을 만들 수 있습니다. 자세한 내용은 [엔터프라이즈 애플리케이션](./../manage-apps/view-applications-portal.md)을 참조하세요.

**Q: 지정 된 사용자 또는 앱에 여러 사용 약관을 추가할 수 있습니까?**<br />
A: 예. 해당 그룹 또는 애플리케이션을 대상으로 하는 조건부 액세스 정책을 여러 개 작성하면 가능합니다. 여러 사용 약관의 범위에서 사용자 떨어지면 번 사용 하 여 하나의 약관에 동의 있습니다.

**Q: 사용자의 사용 약관을 거부 하는 경우 어떻게 되나요?**<br />
A: 사용자는 애플리케이션에 액세스할 수 없습니다. 액세스하려면 다시 로그인하여 약관에 동의해야 합니다.

**Q: 이전에 허용 된 사용 약관 unaccept 수는?**<br />
A: 할 수 있습니다 [검토에는 이전에 사용 약관 수락](#how-users-can-review-their-terms-of-use), 하지만 현재 unaccept에 방법이 없습니다.

**Q: Intune 사용 약관도 사용하면 어떻게 되나요?**<br />
A: 두 Azure AD 사용 약관을 구성한 경우 및 [Intune 약관](/intune/terms-and-conditions-create), 사용자는 둘 다 허용 해야 합니다. 자세한 내용은 [조직에 적합한 약관 솔루션 선택 블로그 게시물](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)을 참조하세요.

**Q: 사용 약관에서 인증을 위해 어떤 끝점이 사용 하나요?**<br />
A: 사용 약관에서 인증을 위해 다음 끝점을 사용 합니다. https://tokenprovider.termsofuse.identitygovernance.azure.com 고 https://account.activedirectory.windowsazure.com입니다. 조직이 등록에 대 한 허용 목록 Url의 경우 이러한 끝점에 대 한 Azure AD 끝점 함께 허용 목록에 로그인을 추가 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: 클라우드 앱에 액세스하기 전에 사용 약관에 동의하도록 요구](require-tou.md)
- [Azure Active Directory의 조건부 액세스 모범 사례](best-practices.md)
