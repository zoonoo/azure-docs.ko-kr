---
title: 이용 약관 - Azure Active Directory | 마이크로 소프트 문서
description: Azure Active Directory 사용 약관을 사용하여 액세스하기 전에 직원 또는 게스트에게 정보를 표시하기 시작하십시오.
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
ms.openlocfilehash: a2f06a7c88a7c17f5f93201192664c2d4a97564e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480966"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory 사용 약관

Azure AD 사용 약관은 조직에서 최종 사용자에게 정보를 표시하는 데 사용할 수 있는 간단한 방법을 제공합니다. 이 프레젠테이션은 사용자가 법률 또는 규정 준수 요구 사항에 대한 관련 고지 사항을 볼 수 있게 해줍니다. 이 문서에서는 사용 약관을 시작하는 방법을 설명합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>개요 비디오

다음 비디오에서는 사용 약관에 대한 간략한 개요를 제공합니다.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

추가 비디오를 보려면 다음을 참조하세요.
- [Azure Active Directory에서 사용 약관을 배포하는 방법](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Azure Active Directory에서 사용 약관을 롤아웃하는 방법](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>사용 약관으로 무엇을 할 수 있습니까?

Azure AD 사용 약관에는 다음과 같은 기능이 있습니다.

- 액세스하기 전에 직원 또는 게스트가 이용 약관에 동의하도록 요구합니다.
- 액세스하기 전에 직원 또는 게스트가 모든 장치에서 사용 약관에 동의하도록 요구합니다.
- 직원 또는 게스트가 반복 일정에 따라 이용 약관에 동의하도록 요구합니다.
- MFA(Azure 다단계 인증)에 보안 정보를 등록하기 전에 직원 또는 게스트가 사용 약관에 동의하도록 요구합니다.
- Azure AD 셀프 서비스 암호 재설정(SSPR)에 보안 정보를 등록하기 전에 직원이 사용 약관에 동의하도록 요구합니다.
- 조직의 모든 사용자에 대한 일반적인 사용 약관을 제시합니다.
- 사용자 속성(예: [동적 그룹](../users-groups-roles/groups-dynamic-membership.md)을 사용하여 의사와 간호사, 국내 직원과 해외 직원에게 맞는 사용 약관을 각각 제공).
- Salesforce와 같이 비즈니스에 미치는 영향이 큰 응용 프로그램에 액세스할 때 특정 사용 약관을 제시합니다.
- 다른 언어로 사용 약관을 제시합니다.
- 이용 약관에 동의했거나 수락하지 않은 사람을 나열합니다.
- 개인 정보 보호 규정을 준수하도록 지원
- 규정 준수 및 감사를 위한 사용 약관 활동 로그를 표시합니다.
- [Microsoft 그래프 API(현재](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) 미리 보기)를 사용하여 사용 약관을 만들고 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure AD 사용 약관을 사용하고 구성하려면 다음이 있어야 합니다.

- Azure AD Premium P1, P2, EMS E3 또는 EMS E5 구독.
   - 이러한 구독 중 하나가 없는 경우 [Azure AD Premium을 받거나](../fundamentals/active-directory-get-started-premium.md)[Azure AD Premium 평가판을 사용](https://azure.microsoft.com/trial/get-started-active-directory/)할 수 있습니다.
- 구성하려는 디렉터리에 대한 다음 관리자 계정 중 하나:
   - 전역 관리자
   - 보안 관리자
   - 조건부 액세스 관리자

## <a name="terms-of-use-document"></a>사용 약관 문서

Azure AD 사용 약관은 PDF 형식을 사용하여 콘텐츠를 표시합니다. PDF 파일은 기존 계약서와 같이 사용자가 로그인하는 동안 최종 사용자 계약을 수집할 수 있는 모든 콘텐츠가 될 수 있습니다. 모바일 디바이스에서 사용자를 지원하기 위한 PDF의 권장 글꼴 크기는 24pt입니다.

## <a name="add-terms-of-use"></a>사용 약관 추가

사용 약관 문서를 완료한 후에는 다음 절차를 사용하여 문서를 추가합니다.

1. Azure에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. [https://aka.ms/catou](https://aka.ms/catou)에서 **사용 약관**으로 이동합니다.

   ![조건부 액세스 - 이용 약관 블레이드](./media/terms-of-use/tou-blade.png)

1. **새 사용 약관**을 클릭합니다.

   ![사용 약관 설정을 지정하는 새 사용 기간 창](./media/terms-of-use/new-tou.png)

1. **이름** 상자에 Azure 포털에서 사용할 사용 약관의 이름을 입력합니다.
1. **표시 이름** 상자에 사용자가 로그인할 때 표시되는 제목을 입력합니다.
1. **이용 약관 문서를**보려면 최종 사용 약관을 찾아PDF를 선택하고 선택하십시오.
1. 사용 약관 문서의 언어를 선택합니다. 언어 옵션을 사용하면 사용 약관을 다양한 언어로 여러 개 업로드할 수 있습니다. 최종 사용자에게 표시되는 사용 약관의 버전은 브라우저 기본 설정에 기반합니다.
1. 최종 사용자가 수락하기 전에 사용 약관을 볼 것을 요구하려면 **사용자가 사용 약관을** **On으로**확장하도록 설정합니다.
1. 최종 사용자가 액세스하는 모든 장치에서 사용 약관에 동의하도록 요구하려면 사용자가 모든 장치에서 **On으로** **동의하도록** 요구합니다. 이 옵션을 사용하도록 설정한 경우 추가 응용 프로그램을 설치해야 할 수 있습니다. 자세한 내용은 [장치별 사용 약관을](#per-device-terms-of-use)참조하십시오.
1. 일정에 따라 사용 약관동의만료를 않으려면 **에** **동의 만료를** 설정합니다. 켜기로 설정되면 두 개의 추가 일정 설정이 표시됩니다.

   ![시작 날짜, 빈도 및 기간을 설정하는 동의 설정 만료](./media/terms-of-use/expire-consents.png)

1. 만료 **시작 및** **빈도** 설정을 사용하여 사용 만료 일정을 지정합니다. 다음 표는 몇 가지 예제 설정의 결과를 보여 줍니다.

   | 만료 시작 날짜 | 빈도 | 결과 |
   | --- | --- | --- |
   | 오늘 날짜  | 매월 | 오늘부터 사용자는 이용 약관에 동의한 다음 매월 다시 수락해야 합니다. |
   | 미래 날짜  | 매월 | 오늘부터 사용자는 이용 약관에 동의해야 합니다. 미래 날짜가 발생하면 동의가 만료되고 이후 사용자는 매월 다시 동의해야 합니다.  |

   예를 들어 만료 시작 날짜를 **1월 1일**로 설정하고 빈도를 **매월**로 설정하는 경우 사용자 두 명에 대한 만료는 다음과 같은 방식으로 발생합니다.

   | 사용자 | 첫 번째 동의 날짜 | 첫 번째 만료 날짜 | 두 번째 만료 날짜 | 세 번째 만료 날짜 |
   | --- | --- | --- | --- | --- |
   | Alice | 1월 1일 | 2월 1일 | 3월 1일 | 4월 1일 |
   | Bob | 1월 15일 | 2월 1일 | 3월 1일 | 4월 1일 |

1. 재수락 전에 기간을 사용하려면 사용자가 사용 약관을 다시 수락하기 까지의 일 수를 지정하기 **위해(일)** 설정이 필요합니다. 이를 통해 사용자는 자신의 일정을 따를 수 있습니다. 예를 들어 기간을 **30**일로 설정하면 두 사용자에 대한 만료가 다음과 같은 방식으로 발생합니다.

   | 사용자 | 첫 번째 동의 날짜 | 첫 번째 만료 날짜 | 두 번째 만료 날짜 | 세 번째 만료 날짜 |
   | --- | --- | --- | --- | --- |
   | Alice | 1월 1일 | 1월 31일 | 3월 2일 | 4월 1일 |
   | Bob | 1월 15일 | 2월 14일 | 3월 16일 | 4월 15일 |

   **동의 만료** 및 **다시 동의가 필요하기 전 기간(일)** 설정을 함께 사용할 수 있지만, 일반적으로 두 설정 중 하나만 사용합니다.

1. **조건부 액세스에서** **조건부 액세스 정책 템플릿** 목록을 사용하여 사용 약관을 적용하기 위해 템플릿을 선택합니다.

   ![정책 템플릿을 선택하기 위한 조건부 액세스 드롭다운 목록](./media/terms-of-use/conditional-access-templates.png)

   | 템플릿 | 설명 |
   | --- | --- |
   | **모든 게스트에 대한 클라우드 앱 액세스** | 모든 게스트 및 모든 클라우드 앱에 대해 조건부 액세스 정책이 만들어집니다. 이 정책은 Azure Portal에 영향을 줍니다. 이 항목이 생성되면 로그아웃했다 로그인해야 할 수 있습니다. |
   | **모든 사용자에 대한 클라우드 앱 액세스** | 모든 사용자 및 모든 클라우드 앱에 대해 조건부 액세스 정책이 만들어집니다. 이 정책은 Azure Portal에 영향을 줍니다. 이 항목이 생성되면 로그아웃했다 로그인해야 할 수 있습니다. |
   | **사용자 지정 정책** | 이 사용 약관에 적용할 사용자, 그룹 및 앱을 선택합니다. |
   | **나중에 조건부 액세스 정책 만들기** | 이 사용 약관은 조건부 액세스 정책을 만들 때 권한 부여 제어 목록에 표시됩니다. |

   >[!IMPORTANT]
   >조건부 액세스 정책 제어(이용 약관 포함)는 서비스 계정의 적용을 지원하지 않습니다. 조건부 액세스 정책에서 모든 서비스 계정을 제외하는 것이 좋습니다.

    사용자 지정 조건부 액세스 정책은 특정 클라우드 응용 프로그램 또는 사용자 그룹까지 세분화된 사용 약관을 사용할 수 있도록 합니다. 자세한 내용은 [빠른 시작: 클라우드 앱에 액세스하기 전에 사용 약관을 수락해야 합니다.](require-tou.md)

1. **만들기**를 클릭합니다.

   사용자 지정 조건부 액세스 템플릿을 선택한 경우 사용자 지정 조건부 액세스 정책을 만들 수 있는 새 화면이 나타납니다.

   ![사용자 지정 조건부 액세스 정책 템플릿을 선택한 경우 새 조건부 액세스 창](./media/terms-of-use/custom-policy.png)

   이제 새 사용 약관이 표시됩니다.

   ![블레이드 사용 약관에 나열된 새로운 사용 약관](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>사용 약관에 동의/거부한 사람에 대한 보고서 보기

[사용 약관] 블레이드에는 사용 약관에 동의한 사용자 및 거부한 사용자 수가 표시됩니다. 이러한 수와 수락/거부자는 이용 약관의 수명 동안 저장됩니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.

   ![사용자 표시 수를 나열하는 사용 블레이드 의 약관은 수락 및 거부](./media/terms-of-use/view-tou.png)

1. 사용 약관을 보려면 **수락** 또는 **거부** 됨 아래의 숫자를 클릭하여 사용자의 현재 상태를 확인합니다.

   ![수락한 사용자를 나열하는 이용 약관 동의 창](./media/terms-of-use/accepted-tou.png)

1. 개별 사용자의 기록을 보려면 줄임표(**...**)를 클릭한 후 **기록 보기**를 클릭합니다.

   ![사용자에 대한 기록 컨텍스트 메뉴 보기](./media/terms-of-use/view-history-menu.png)

   [기록 보기] 창에서 모든 승인, 거부 및 만료 기록을 확인합니다.

   ![보기 기록 창에 사용자에 대 한 허용, 거절 및 만료 기록 나열](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Azure AD 감사 로그 보기

추가 활동을 보려면 Azure AD 사용 약관에는 감사 로그가 포함됩니다. 각 사용자 동의는 **30일** 동안 저장된 감사 로그에서 이벤트를 트리거합니다. 포털에서 이러한 로그를 살펴보거나 .csv 파일로 다운로드할 수 있습니다.

Azure AD 감사 로그를 시작하려면 다음 절차를 따릅니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.
1. 이용 약관을 선택합니다.
1. **감사 로그 보기**를 클릭합니다.

   ![감사 로그 보기 옵션이 강조 표시된 사용 블레이드](./media/terms-of-use/audit-tou.png)

1. Azure AD 감사 로그 화면에서 제공된 목록을 사용하여 특정 감사 로그 정보를 대상으로 하도록 정보를 필터링할 수 있습니다.

   **다운로드**를 클릭하여 로컬에서 사용하도록 정보를 .csv 파일로 다운로드할 수도 있습니다.

   ![Azure AD 감사 로그 화면 목록 날짜, 대상 정책, 시작 및 활동](./media/terms-of-use/audit-logs-tou.png)

   로그를 클릭하면 추가 활동 세부 정보가 포함된 창이 나타납니다.

   ![활동 표시 로그에 대한 활동 세부 정보, 활동 상태( 시작됨, 대상 정책)](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>사용자에 대한 사용 약관의 모습

이용 약관이 만들어지고 적용되면 범위에 있는 사용자는 로그인 하는 동안 다음 화면을 볼 수 있습니다.

![사용자가 에 서명할 때 나타나는 사용 약관 예제](./media/terms-of-use/user-tou.png)

사용자는 사용 약관을 볼 수 있으며 필요한 경우 단추를 사용하여 확대/축소할 수 있습니다.

![확대/축소 버튼사용 약관 보기](./media/terms-of-use/zoom-buttons.png)

다음 화면에서는 모바일 기기에서 사용 약관이 어떻게 표시되는지 보여 주며, 사용 약관이 어떻게 보이는지 보여 주며, 이 화면은 다음과 같습니다.

![사용자가 모바일 기기에 로그인할 때 표시되는 사용 약관 예제](./media/terms-of-use/mobile-tou.png)

사용자는 한 번만 이용 약관에 동의해야 하며 이후 로그인 시 사용 약관을 다시 볼 수 없습니다.

### <a name="how-users-can-review-their-terms-of-use"></a>사용자가 이용 약관을 검토할 수 있는 방법

사용자는 다음 절차를 사용하여 수락한 이용 약관을 검토하고 확인할 수 있습니다.

1. 에 로그인합니다. [https://myapps.microsoft.com](https://myapps.microsoft.com)
1. 오른쪽 위 모서리에서 자신의 이름을 클릭하고 **프로필**을 선택합니다.

   ![사용자의 창이 열려 있는 MyApps 사이트](./media/terms-of-use/tou14.png)

1. 프로필 페이지에서 **사용 약관 검토**를 클릭합니다.

   ![사용 약관 검토 링크를 표시하는 사용자의 프로필 페이지](./media/terms-of-use/tou13a.png)

1. 여기에서 사용자가 동의한 사용 약관을 검토할 수 있습니다.

## <a name="edit-terms-of-use-details"></a>이용 약관 세부 정보 편집

사용 약관에 대한 세부 정보를 수정할 수는 있지만 기존 문서는 수정할 수 없습니다. 다음 절차에서는 세부 정보를 편집하는 방법에 대해 설명합니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.
1. 편집할 사용 약관을 선택합니다.
1. **사용 약관 편집**을 클릭합니다.
1. [사용 약관 편집] 창에서 이름을 변경하거나, 이름을 표시하거나, 사용자가 값을 확장하도록 요구합니다.

   PDF 문서와 같이 변경하려는 다른 설정이 있는 경우 사용자가 모든 장치에 동의하도록 요구하거나, 동의가 만료되거나, 재수락 전 의 기간 또는 조건부 액세스 정책을 만들어야 합니다.

   ![이름 표시 및 확장 옵션 사용 창 편집](./media/terms-of-use/edit-tou.png)

1. **저장**을 클릭하여 변경 내용을 저장합니다.

   변경 내용이 저장된 후 사용자가 이러한 편집 내용에 다시 동의할 필요가 없습니다.

## <a name="add-a-terms-of-use-language"></a>이용 약관 언어 추가

다음 절차에서 사용 약관 언어를 추가하는 방법을 설명합니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.
1. 편집할 사용 약관을 선택합니다.
1. 세부 정보 창에서 **언어** 탭을 클릭합니다.

   ![세부 정보 창에 언어 탭을 선택하여 표시하기 위한 사용 약관](./media/terms-of-use/languages-tou.png)

1. **언어 추가**를 클릭합니다.
1. [사용 약관 언어 추가] 창에서 지역화된 PDF를 업로드하고 언어를 선택합니다.

   ![지역화된 PDF를 업로드하는 옵션과 함께 사용 약관 언어 창 추가](./media/terms-of-use/language-add-tou.png)

1. **추가**를 클릭하여 언어를 추가합니다.

## <a name="per-device-terms-of-use"></a>장치별 사용 약관

사용자가 **모든 장치 설정에 동의하도록 요구할** 경우 최종 사용자가 액세스하는 모든 장치에서 사용 약관에 동의하도록 요구할 수 있습니다. 최종 사용자는 Azure AD에 장치를 등록해야 합니다. 장치가 등록되면 장치 ID가 각 장치에서 사용 약관을 적용하는 데 사용됩니다.

지원되는 플랫폼 및 소프트웨어 목록은 다음과 같습니다.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | 윈도우 10 | 기타 |
> | --- | --- | --- | --- | --- |
> | **네이티브 앱** | yes | yes | yes |  |
> | **Microsoft Edge** | yes | yes | yes |  |
> | **Internet Explorer** | yes | yes | yes |  |
> | **Chrome(확장 포함)** | yes | yes | yes |  |

장치별 사용 약관에는 다음과 같은 제약 조건이 있습니다.

- 디바이스는 하나의 테넌트에만 연결될 수 있습니다.
- 사용자는 해당 디바이스에 연결할 권한이 있어야 합니다.
- Intune 등록 앱이 지원되지 않습니다.
- Azure AD B2B 사용자는 지원되지 않습니다.

사용자의 디바이스가 연결되지 않으면 해당 디바이스에 연결해야 한다는 메시지가 표시됩니다. 해당 환경은 플랫폼 및 소프트웨어에 따라 다릅니다.

### <a name="join-a-windows-10-device"></a>Windows 10 디바이스 연결

사용자가 Windows 10 및 Microsoft Edge를 사용하는 경우 [해당 디바이스에 연결](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device)하기 위해 다음과 같은 메시지가 표시됩니다.

![윈도우 10 및 마이크로소프트 가장자리 - 장치를 나타내는 메시지 등록 해야 합니다.](./media/terms-of-use/per-device-win10-edge.png)

Chrome을 사용하는 경우 [Windows 10 계정 확장](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)을 설치할지 묻는 메시지가 표시됩니다.

### <a name="register-an-ios-device"></a>iOS 기기 등록

사용자가 iOS 장치를 사용하는 경우 [Microsoft 인증기 앱을](https://apps.apple.com/us/app/microsoft-authenticator/id983156458)설치하라는 메시지가 표시됩니다.

### <a name="register-an-android-device"></a>안드로이드 기기 등록

사용자가 Android 장치를 사용하는 경우 [Microsoft 인증자 앱을](https://play.google.com/store/apps/details?id=com.azure.authenticator)설치하라는 메시지가 표시됩니다.

### <a name="browsers"></a>브라우저

사용자가 지원되지 않는 브라우저를 사용하는 경우에는 다른 브라우저를 사용하도록 요청하는 메시지가 표시됩니다.

![장치를 나타내는 메시지가 등록되어야 하지만 브라우저가 지원되지 않음](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>사용 약관 삭제

다음 절차를 사용하여 이전 사용 약관을 삭제할 수 있습니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.
1. 제거할 사용 약관을 선택합니다.
1. **사용 약관 삭제**를 클릭합니다.
1. 계속 진행할 것인지 묻는 메시지가 표시되면 **예**를 클릭합니다.

   ![이용 약관 삭제 확인을 요청하는 메시지](./media/terms-of-use/delete-tou.png)

   더 이상 사용 약관이 표시되지 않습니다.

## <a name="deleted-users-and-active-terms-of-use"></a>삭제된 사용자 및 활성 사용 약관

기본적으로 삭제된 사용자는 30일 동안 Azure AD에 삭제된 상태로 남아 있지만, 이 기간 동안 관리자는 필요한 경우 삭제된 사용자를 복원할 수 있습니다. 30일 후 해당 사용자가 영구적으로 삭제됩니다. 또한 Azure Active Directory 포털을 사용하여 전역 관리자는 해당 기간이 만료되기 전에 명시적으로 [최근에 삭제된 사용자를 영구적으로 삭제](../fundamentals/active-directory-users-restore.md)할 수 있습니다. 사용자가 영구적으로 삭제된 경우 해당 사용자에 대한 후속 데이터가 활성 사용 약관에서 제거됩니다. 삭제된 사용자에 대한 감사 정보는 감사 로그에 남아 있습니다.

## <a name="policy-changes"></a>정책 변경 내용

조건부 액세스 정책은 즉시 적용됩니다. 이 경우 관리자에게 "sad 클라우드" 또는 "Azure AD 토큰 문제"가 표시되기 시작합니다. 관리자는 새 정책을 충족하기 위해 로그아웃했다가 다시 로그인해야 합니다.

> [!IMPORTANT]
> 범위에 있는 사용자는 다음과 같은 경우 새 정책을 충족하기 위해 로그아웃 및 로그인해야 합니다.
>
> - 조건부 액세스 정책은 사용 약관에 따라 활성화됩니다.
> - 또는 두 번째 사용 약관이 작성된 경우

## <a name="b2b-guests-preview"></a>B2B 게스트(미리 보기)

대부분의 조직은 직원들이 조직의 사용 약관 및 개인 정보 취급 방침에 동의할 수 있는 프로세스를 마련하고 있습니다. 그러나 SharePoint 또는 Teams를 통해 추가될 경우 Azure AD B2B 게스트에게 동일한 동의를 어떻게 적용할 수 있나요? 조건부 액세스 및 이용 약관을 사용하여 B2B 게스트 사용자에게 직접 정책을 적용할 수 있습니다. 초대 교환 흐름 중에 사용자에게 사용 약관이 표시됩니다. 이 지원은 현재 미리 보기로 제공되고 있습니다.

사용 약관은 사용자에게 Azure AD의 게스트 계정이 있는 경우에만 표시됩니다. SharePoint Online은 현재 사용자에게 게스트 계정이 필요하지 않은 문서 또는 폴더를 공유하는 [임시 외부 공유 받는](/sharepoint/what-s-new-in-sharing-in-targeted-release) 사람 환경을 제공합니다. 이 경우 이용 약관이 표시되지 않습니다.

![사용자 및 그룹 창 - 모든 게스트 사용자 옵션이 선택된 탭 포함](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>클라우드 앱 지원(미리 보기)

사용 약관은 Azure Information Protection 및 Microsoft Intune과 같은 다른 클라우드 앱에 사용할 수 있습니다. 이 지원은 현재 미리 보기로 제공되고 있습니다.

### <a name="azure-information-protection"></a>Azure Information Protection

Azure 정보 보호 앱에 대한 조건부 액세스 정책을 구성할 수 있으며 사용자가 보호된 문서에 액세스할 때 사용 약관이 필요합니다. 이렇게 하면 사용자가 보호된 문서에 처음으로 액세스하기 전에 사용 약관이 트리거됩니다.

![Microsoft Azure 정보 보호 앱을 선택한 클라우드 앱 창](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune 등록

Microsoft Intune 등록 앱에 대한 조건부 액세스 정책을 구성할 수 있으며 Intune에 장치를 등록하기 전에 사용 약관이 필요합니다. 자세한 내용은 [조직에 적합한 약관 솔루션 선택 블로그 게시물](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409) 읽기를 참조하세요.

![선택한 Microsoft Intune 앱으로 클라우드 앱 창](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Intune 등록 앱은 [장치별 사용 약관에](#per-device-terms-of-use)대해 지원되지 않습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

**Q: 사용자가 이용 약관을 수락한 시기/경우 어떻게 확인할 수 있습니까?**<br />
A: 사용 약관 블레이드에서 **수락** 아래에 있는 숫자를 클릭합니다. Azure AD 감사 로그에서 동의 작업을 보거나 검색할 수도 있습니다. 자세한 내용은 사용 약관에 동의/거부한 사람에 대한 보고서 보기 및 [Azure AD 감사 로그 보기](#view-azure-ad-audit-logs)를 참조하세요.

**Q: 정보가 얼마나 오래 저장되나요?**<br />
A: 이용 약관에 사용자가 계산되며 수락/거부된 사람은 사용 약관의 수명 동안 저장됩니다. Azure AD 감사 로그는 30일 동안 저장됩니다.

**Q: 사용 보고서와 Azure AD 감사 로그에서 다른 수의 동의가 표시되는 이유는 무엇입니까?**<br />
A: 사용 약관 보고서는 해당 사용 약관의 수명 동안 저장되고 Azure AD 감사 로그는 30일 동안 저장됩니다. 또한 이용 약관 보고서는 사용자의 현재 동의 상태만 표시합니다. 예를 들어 사용자가 거부한 다음 수락하면 사용 약관 보고서에 해당 사용자의 동의만 표시됩니다. 기록을 확인해야 하는 경우, Azure AD 감사 로그를 사용할 수 있습니다.

**Q: 이용 약관에 대한 세부 정보를 편집하는 경우 사용자가 다시 수락해야 합니까?**<br />
A: 아니요, 관리자가 사용 약관(이름, 표시 이름, 사용자가 확장 또는 언어 추가필요)에 대한 세부 정보를 편집하는 경우 사용자가 새 약관을 다시 수락할 필요가 없습니다.

**Q: 기존 사용 약관 문서를 업데이트할 수 있습니까?**<br />
A: 현재 기존 사용 약관 문서를 업데이트할 수 없습니다. 사용 약관 문서를 변경하려면 새 사용 조건 인스턴스를 만들어야 합니다.

**Q: 하이퍼링크가 사용 PDF 문서에 있는 경우 최종 사용자가 하이퍼링크를 클릭할 수 있습니까?**<br />
A: 예. 최종 사용자는 추가 페이지에 대한 하이퍼링크를 선택할 수 있지만 문서 내의 섹션에 대한 링크는 지원되지 않습니다.

**Q: 사용 약관이 여러 언어를 지원할 수 있습니까?**<br />
A: 예. 현재 관리자가 단일 사용 약관에 대해 구성할 수 있는 언어는 108개입니다. 관리자는 여러 PDF 문서를 업로드하고 해당 언어를 사용하는 문서에 태그를 지정할 수 있습니다(최대 108개). 최종 사용자가 로그인할 때 브라우저 언어 기본 설정이 확인되고 일치하는 문서가 표시됩니다. 일치 항목이 없으면 업로드된 첫 번째 문서인 기본 문서가 표시됩니다.

**Q: 사용 약관은 언제 트리거되나요?**<br />
A: 사용 약관은 로그인 환경 중에 트리거됩니다.

**Q: 사용 약관을 대상으로 지정할 수 있는 애플리케이션은 무엇인가요?**<br />
A: 최신 인증을 사용하여 엔터프라이즈 응용 프로그램에 조건부 액세스 정책을 만들 수 있습니다. 자세한 내용은 [엔터프라이즈 애플리케이션](./../manage-apps/view-applications-portal.md)을 참조하세요.

**Q: 지정된 사용자 또는 앱에 여러 사용 약관을 추가할 수 있습니까?**<br />
A: 예. 이러한 그룹 또는 응용 프로그램을 대상으로 하는 여러 조건부 액세스 정책을 만들어 야 합니다. 사용자가 여러 사용 약관의 범위에 속하는 경우 한 번에 하나의 사용 약관에 동의합니다.

**Q: 사용자가 이용 약관을 거부하면 어떻게 됩니까?**<br />
A: 사용자는 애플리케이션에 액세스할 수 없습니다. 액세스하려면 다시 로그인하여 약관에 동의해야 합니다.

**Q: 이전에 수락된 이용 약관을 수락취소할 수 있습니까?**<br />
A: [이전에 수락된 이용 약관을 검토할](#how-users-can-review-their-terms-of-use)수 있지만 현재는 수락을 거부할 수 있는 방법이 없습니다.

**Q: Intune 사용 약관도 사용하면 어떻게 되나요?**<br />
A: Azure AD 이용 약관과 [Intune 이용 약관을](/intune/terms-and-conditions-create)모두 구성한 경우 사용자는 두 가지 를 모두 수락해야 합니다. 자세한 내용은 [조직에 적합한 약관 솔루션 선택 블로그 게시물](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)을 참조하세요.

**Q: 서비스 이용 약관은 인증을 위해 어떤 끝점을 사용합니까?**<br />
A: 이용 약관은 인증에 https://tokenprovider.termsofuse.identitygovernance.azure.com 다음 끝점을 https://account.activedirectory.windowsazure.com사용합니다. 조직에 등록을 위한 URL 허용 목록이 있는 경우 로그인할 Azure AD 끝점과 함께 허용 목록에 이러한 끝점을 추가해야 합니다.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: 클라우드 앱에 액세스하기 전에 사용 약관에 동의하도록 요구](require-tou.md)
- [Azure Active 디렉터리에서 조건부 액세스에 대한 모범 사례](best-practices.md)
