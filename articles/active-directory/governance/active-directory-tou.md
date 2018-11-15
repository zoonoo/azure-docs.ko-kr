---
title: Azure Active Directory 사용 약관 | Microsoft Docs
description: Azure AD 사용 약관은 귀하와 귀하의 회사가 Azure AD 서비스 사용자에게 사용 약관을 제공할 수 있도록 허용합니다.
services: active-directory
author: rolyon
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 11/02/2018
ms.author: rolyon
ms.openlocfilehash: 8fddcdbb8aa523cf3a98a8f2b203440ceedbdf06
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51015212"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Azure Active Directory 사용 약관 기능
Azure AD 사용 약관은 조직이 최종 사용자에게 정보를 제공하는 데 사용할 수 있는 간단한 방법을 제공합니다. 이 프레젠테이션은 사용자가 법률 또는 규정 준수 요구 사항에 대한 관련 고지 사항을 볼 수 있게 해줍니다. 이 문서는 사용 약관을 시작하는 방법을 설명합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>개요 비디오

다음 비디오는 사용 약관의 간략한 개요를 제공합니다.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

추가 비디오를 보려면 다음을 참조하세요.
- [Azure Active Directory의 사용 약관 배포 방법](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Azure Active Directory의 사용 약관 롤아웃 방법](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>사용 약관으로 무엇을 할 수 있나요?
Azure AD 사용 약관으로 다음과 같은 일을 할 수 있습니다.
- 직원 또는 게스트가 서비스에 액세스하려면 사용 약관에 동의하도록 요구.
- 조직의 모든 사용자에 대한 일반 사용 약관 제공.
- 사용자 특성을 기반으로 하는 특정 사용 약관 제공(예: [동적 그룹](../users-groups-roles/groups-dynamic-membership.md)을 사용하여 의사와 간호사, 국내 직원과 해외 직원에게 맞는 사용 약관을 각각 제공).
- Salesforce 같은 비즈니스 영향력이 높은 응용 프로그램에 액세스할 때 특정 사용 약관 제공.
- 여러 언어로 사용 약관 제공.
- 개인 정보 보호 규정을 준수하도록 지원
- 사용 약관에 동의한 또는 동의하지 않은 사용자 나열.
- 준수 및 감사를 위해 사용 약관 활동 로그 표시
- [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement)를 사용하여 사용 약관을 만들고 관리합니다(현재 미리 보기로 제공).

## <a name="prerequisites"></a>필수 조건
Azure AD 사용 약관을 사용하고 구성하려면 다음이 필요합니다.

- Azure AD Premium P1, P2, EMS E3 또는 EMS E5 구독.
    - 이러한 구독 중 하나가 없는 경우 [Azure AD Premium을 받거나](../fundamentals/active-directory-get-started-premium.md) [Azure AD Premium 평가판을 사용](https://azure.microsoft.com/trial/get-started-active-directory/)할 수 있습니다.
- 구성하려는 디렉터리에 대한 다음 관리자 계정 중 하나:
    - 전역 관리자
    - 보안 관리자
    - 조건부 액세스 관리자

## <a name="terms-of-use-document"></a>사용 약관 문서

Azure AD 사용 약관은 PDF 형식을 사용하여 콘텐츠를 제공합니다. PDF 파일은 기존 계약서와 같이 사용자가 로그인하는 동안 최종 사용자 계약을 수집할 수 있는 모든 콘텐츠가 될 수 있습니다. PDF의 권장 글꼴 크기는 24입니다.

## <a name="add-terms-of-use"></a>사용 약관 추가
사용 약관 문서를 완성했으면 다음 절차에 따라 추가합니다.

1. Azure에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 **사용 약관**으로 이동합니다.

    ![사용 약관 블레이드](./media/active-directory-tou/tou-blade.png)

1. **새 사용 약관**을 클릭합니다.

    ![TOU 추가](./media/active-directory-tou/new-tou.png)

1. 사용 약관의 **이름**을 입력합니다.

2. **표시 이름**을 입력합니다.  이 헤더는 사용자가 로그인할 때 표시됩니다.

3. 완료된 사용 약관 PDF를 **찾아** 선택합니다.

4. 사용 약관의 언어를 **선택**합니다.  언어 옵션을 사용하면 사용 약관을 다양한 언어로 여러 개 업로드할 수 있습니다.  최종 사용자에게 표시되는 사용 약관의 버전은 브라우저 기본 설정에 기반합니다.

5. **사용자가 사용 약관을 확장해야 함**을 켬 또는 끔으로 선택합니다.  이 설정을 켜면 최종 사용자가 사용 약관에 동의하기 전에 사용 약관을 봐야 합니다.

6. **조건부 액세스**에서 드롭다운 목록의 템플릿 또는 사용자 지정 조건부 액세스 정책을 선택하여 업로드된 사용 약관을 **강제 적용**할 수 있습니다.  사용자 지정 조건부 액세스 정책을 통해 특정 클라우드 응용 프로그램 또는 사용자 그룹에까지 세분화된 사용 약관을 설정할 수 있습니다.  자세한 내용은 [조건부 액세스 정책 구성](../../cognitive-services/qnamaker/concepts/best-practices.md)을 참조하세요.

    >[!IMPORTANT]
    >조건부 액세스 정책 제어(사용 조건 포함)는 서비스 계정에 적용을 지원하지 않습니다.  우리는 조건부 액세스 정책에서 모든 서비스 계정을 배제하도록 추천합니다.

7. **만들기**를 클릭합니다.

8. 사용자 지정 조건부 액세스 템플릿을 선택한 경우 조건부 액세스 정책을 사용자 지정할 수 있는 새 화면이 나타납니다.

    이제 새 사용 약관이 표시됩니다.

    ![TOU 추가](./media/active-directory-tou/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>사용 약관에 동의/거부한 사람에 대한 보고서 보기
[사용 약관] 블레이드에는 사용 약관에 동의한 사용자 및 거부한 사용자 수가 표시됩니다. 이러한 사용자 수 및 동의/거부한 사용자 명단은 사용 약관의 수명 기간 동안 저장됩니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.

    ![감사 이벤트](./media/active-directory-tou/view-tou.png)

1. **동의** 또는 **거부** 아래의 숫자를 클릭하면 사용자의 현재 상태를 볼 수 있습니다.

    ![감사 이벤트](./media/active-directory-tou/accepted-tou.png)

## <a name="view-azure-ad-audit-logs"></a>Azure AD 감사 로그 보기
추가 작업을 확인할 수 있도록 Azure AD 사용 약관에 감사 로그가 포함되어 있습니다. 각 사용자 동의는 30일 동안 저장된 감사 로그에서 이벤트를 트리거합니다. 포털에서 이러한 로그를 살펴보거나 .csv 파일로 다운로드할 수 있습니다.

Azure AD 감사 로그를 시작하려면 다음 절차를 따릅니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.

1. **감사 로그 보기**를 클릭합니다.

    ![감사 이벤트](./media/active-directory-tou/audit-tou.png)

1. Azure AD 감사 로그 화면에서 제공된 드롭다운 목록을 사용하여 특정 감사 로그 정보를 대상으로 하도록 정보를 필터링할 수 있습니다.

    ![감사 이벤트](./media/active-directory-tou/audit-logs-tou.png)

1. **다운로드**를 클릭하여 로컬에서 사용하도록 정보를 .csv 파일로 다운로드할 수도 있습니다.

## <a name="what-terms-of-use-looks-like-for-users"></a>사용자에게 표시되는 사용 약관의 모습
사용 약관을 만들어서 적용하면 범위 내에 있는 사용자는 로그인하는 동안 다음과 같은 화면을 볼 수 있습니다.

![감사 이벤트](./media/active-directory-tou/user-tou.png)

다음 화면은 모바일 장치에서 사용 약관이 어떻게 표시되는지 보여줍니다.

![감사 이벤트](./media/active-directory-tou/mobile-tou.png)

사용자는 한 번만 사용 약관에 동의해야 하며 후속 로그인 시 다시 사용 약관이 표시되지 않습니다.

### <a name="how-users-can-review-their-terms-of-use"></a>사용자가 사용 약관을 검토하는 방법
사용자는 다음 절차를 수행하여 자신이 동의한 사용 약관을 검토하고 살펴볼 수 있습니다.

1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에 로그인합니다.

1. 오른쪽 위 모서리에서 자신의 이름을 클릭하고 드롭다운에서 **프로필**을 선택합니다.

    ![프로필](./media/active-directory-tou/tou14.png)

1. 프로필 페이지에서 **사용 약관 검토**를 클릭합니다.

    ![감사 이벤트](./media/active-directory-tou/tou13a.png)

1. 여기서 사용자가 동의한 사용 약관을 검토할 수 있습니다. 

## <a name="edit-terms-of-use-details"></a>사용 약관 세부 정보 편집
사용 약관 세부 정보 중 일부는 편집할 수 있지만 기존 문서는 수정할 수 없습니다. 다음 절차에서는 세부 정보를 편집하는 방법에 대해 설명합니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.

1. 편집하려는 사용 약관을 선택합니다.

1. **사용 약관 편집**을 클릭합니다.

1. [사용 약관 편집] 창에서 이름을 변경하거나, 이름을 표시하거나, 사용자가 값을 확장하도록 요구합니다.

    ![TOU 추가](./media/active-directory-tou/edit-tou.png)

1. **저장**을 클릭하여 변경 내용을 저장합니다.

    변경 내용이 저장되면 사용자는 새 약관을 다시 동의해야 합니다.

## <a name="add-a-terms-of-use-language"></a>사용 약관 언어 추가
다음 절차에서는 사용 약관 언어를 추가하는 방법에 대해 설명합니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.

1. 편집하려는 사용 약관을 선택합니다.

1. 세부 정보 창에서 **언어** 탭을 클릭합니다.

    ![TOU 추가](./media/active-directory-tou/languages-tou.png)

1. **언어 추가**를 클릭합니다.

1. [사용 약관 언어 추가] 창에서 지역화된 PDF를 업로드하고 언어를 선택합니다.

    ![TOU 추가](./media/active-directory-tou/language-add-tou.png)

1. **추가**를 클릭하여 언어를 추가합니다.

## <a name="delete-terms-of-use"></a>사용 약관 삭제
다음 절차에 따라 이전 사용 약관을 삭제할 수 있습니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.

1. 제거할 사용 약관을 선택합니다.

1. **사용 약관 삭제**를 클릭합니다.

1. 계속 진행할 것인지 묻는 메시지가 표시되면 **예**를 클릭합니다.

    ![TOU 추가](./media/active-directory-tou/delete-tou.png)

    사용 약관이 더 이상 표시되지 않습니다.

## <a name="deleted-users-and-active-terms-of-use"></a>삭제된 사용자 및 활성 사용 약관
기본적으로 삭제된 사용자는 30일 동안 Azure AD에 삭제된 상태로 남아 있지만, 이 기간 동안 관리자는 필요한 경우 삭제된 사용자를 복원할 수 있습니다.  30일 후 해당 사용자가 영구적으로 삭제됩니다.  또한 Azure Active Directory 포털을 사용하여 전역 관리자는 해당 기간이 만료되기 전에 명시적으로 [최근에 삭제된 사용자를 영구적으로 삭제](../fundamentals/active-directory-users-restore.md)할 수 있습니다.  사용자가 영구적으로 삭제됐으므로 추후 해당 사용자에 대한 데이터가 현재 사용 약관에서 제거됩니다.  삭제된 사용자에 대한 감사 정보는 감사 로그에 남아 있습니다.

## <a name="policy-changes"></a>정책 변경 내용
조건부 액세스 정책은 즉시 적용됩니다. 이 경우 관리자에게 "sad 클라우드" 또는 "Azure AD 토큰 문제"가 표시되기 시작합니다. 관리자는 새 정책을 충족하기 위해 로그아웃했다가 다시 로그인해야 합니다.

>[!IMPORTANT]
> 범위에 있는 사용자는 다음과 같은 경우 새 정책을 충족하기 위해 로그아웃 및 로그인해야 합니다.
> - 사용 약관에 조건부 액세스 정책이 설정된 경우
> - 또는 두 번째 사용 약관이 작성된 경우

## <a name="frequently-asked-questions"></a>질문과 대답

**Q: 사용자가 사용 약관에 동의했는지 어떻게 볼 수 있나요?**</br>
A: 사용 약관 블레이드에서 **수락** 아래에 있는 숫자를 클릭합니다. Azure AD 감사 로그에서 동의 작업을 보거나 검색할 수도 있습니다. 자세한 내용은 [사용 약관에 동의/거부한 사람에 대한 보고서 보기](#view-who-has-accepted-and-declined) 및 [Azure AD 감사 로그 보기](#view-azure-ad-audit-logs)를 참조하세요.

**Q: 정보가 얼마나 오래 저장되나요?**</br>
A: 사용 약관 보고서에 있는, 사용 약관에 동의/거부한 사용자 수는 사용 약관의 수명 기간 동안 저장됩니다. Azure AD 감사 로그는 30일 동안 저장됩니다.

**Q: 사용 약관 보고서와 Azure AD 감사 로그에 표시되는 동의 수가 다른 이유는 무엇인가요?**</br>
A: 사용 약관 보고서는 사용 약관의 수명 기간 동안 저장되는 반면, Azure AD 감사 로그는 30일 동안 저장됩니다. 또한 사용 약관 보고서에는 사용자의 현재 동의 상태만 표시됩니다. 예를 들어, 사용자가 거부한 후 동의할 경우, 사용 약관 보고서에는 해당 사용자의 동의만 표시됩니다. 기록을 확인해야 하는 경우, Azure AD 감사 로그를 사용할 수 있습니다.

**Q: 사용 약관에 대한 세부 정보를 편집하는 경우 사용자가 다시 동의해야 하나요?**</br>
A: 예, 관리자가 사용 약관의 세부 정보를 편집하면 사용자가 새 사용 약관에 다시 동의해야 합니다.

**Q: 기존 사용 약관 문서는 업데이트할 수 있나요?**</br>
A: 기존 사용 약관 문서는 현재 업데이트할 수 없습니다. 사용 약관 문서를 변경하려면 새 사용 약관 인스턴스를 만들어야 합니다.

**Q: 사용 약관 PDF 문서에 하이퍼링크가 있는 경우, 최종 사용자가 하이퍼링크를 클릭할 수 있나요?**</br>
A: PDF는 기본적으로 JPEG로 렌더링되므로 하이퍼링크를 클릭할 수 없습니다. 사용자는 **보는 데 문제가 있나요? 여기를 클릭하세요.** 를 선택하여 하이퍼링크가 지원되는 네이티브 PDF를 렌더링할 수 있습니다.

**Q: 사용 약관에서 다중 언어를 지원할 수 있나요?**</br>
A: 예. 현재 관리자가 단일 사용 약관에 대해 구성할 수는 108개의 다른 언어가 있습니다.

**Q: 사용 약관은 언제 트리거되나요?**</br>
A: 사용 약관은 로그인 환경 중에 트리거됩니다.

**Q: 사용 약관을 대상으로 지정할 수 있는 응용 프로그램은 무엇인가요?**</br>
A: 최신 인증을 사용하는 엔터프라이즈 응용 프로그램에 조건부 액세스 정책을 만들 수 있습니다.  자세한 내용은 [엔터프라이즈 응용 프로그램](./../manage-apps/view-applications-portal.md)을 참조하세요.

**Q: 특정 사용자 또는 앱에 여러 사용 약관을 추가할 수 있나요?**</br>
A: 예, 해당 그룹 또는 응용 프로그램을 대상으로 하는 조건부 액세스 정책을 여러 개 작성하면 가능합니다. 사용자가 여러 사용 약관 범위에 속하는 경우 한 번에 하나의 사용 약관에 동의합니다.

**Q: 사용자가 사용 약관을 거부하는 경우 어떻게 되나요?**</br>
A: 사용자는 응용 프로그램에 액세스할 수 없습니다. 액세스할 수 있으려면 다시 로그인하여 약관에 동의해야 합니다.

**Q: 이전에 동의한 사용 약관의 수락을 취소할 수 있나요?**</br>
A: [이전에 수락한 사용 약관을 검토](#how-users-can-review-their-terms-of-use)할 수 있지만 현재 수락을 취소할 방법이 없습니다.

**Q: Intune 사용 약관도 사용하면 어떻게 되나요?**</br>
A: Azure AD 사용 약관과 [Intune 약관](/intune/terms-and-conditions-create)을 모두 구성한 경우 사용자는 두 약관에 모두 동의해야 합니다. 자세한 내용은 [조직에 적합한 약관 솔루션 선택 블로그 게시물](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory의 조건부 액세스 모범 사례](../../active-directory/conditional-access/best-practices.md)
