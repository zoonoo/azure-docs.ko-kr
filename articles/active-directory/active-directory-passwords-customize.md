---
title: "사용자 지정: Azure AD 암호 관리 | Microsoft Docs"
description: "요구 사항에 맞게 Azure AD에서 암호 관리의 모양과 느낌, 동작 및 알림을 사용자 지정하는 방법."
services: active-directory
documentationcenter: 
author: asteen
manager: femila
editor: curtand
ms.assetid: 2cddd150-8747-447a-a7cf-1d7d5775c0b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e11be2489d8c0585e386669f34012964e57978d7


---
# <a name="customizing-password-management-to-fit-your-organizations-needs"></a>해당 조직의 요구에 맞게 사용자 지정 암호 관리
> [!IMPORTANT]
> **로그인하는 데 문제가 있나요?** 그렇다면 [암호를 변경하고 재설정하는 방법은 다음과 같습니다](active-directory-passwords-update-your-own-password.md).
> 
> 

사용자에게 가능한 최상의 환경을 제공하려면, 사용자가 사용할 수 있는 모든 암호 관리 구성 옵션을 탐색하고 재생하는 것이 좋습니다. 실제로 **Azure 클래식 포털** 에 있는 [Active Directory 확장](https://manage.windowsazure.com)의 구성 탭으로 이동하여 이 작업을 지금 바로 시작할 수 있습니다. 이 항목에서는 다음을 포함하여 **Azure 클래식 포털** 내 디렉터리의 [구성](https://manage.windowsazure.com)내에서 관리자로 만들 수 있는 다른 암호 관리 사용자 지정을 모두 안내합니다.

| 항목 |  |
| --- | --- |
| 암호 재설정을 사용하거나 사용하지 않도록 설정하려면 어떻게 해야 하나요? |[설정: 암호 재설정을 위해 사용할 수 있는 사용자](#users-enabled-for-password-reset) |
| 암호 재설정을 특정 사용자 집합으로 범위를 지정하려면 어떻게 해야 하나요? |[암호 재설정을 특정 사용자로 제한](#restrict-access-to-password-reset) |
| 지원되는 인증 방법을 변경하려면 어떻게 해야 하나요? |[설정: 사용자에게 제공하는 인증 방법](#authentication-methods-available-to-users) |
| 필수 인증 방법의 수를 변경하려면 어떻게 해야 하나요? |[설정: 필수 인증 방법의 수](#number-of-authentication-methods-required) |
| 사용자 지정 보안 질문을 설정하려면 어떻게 해야 하나요? |[설정: 사용자 지정 보안 질문](#custom-security-questions) |
| 미리 만든 보안 질문을 지역화하여 설정하려면 어떻게 해야 하나요? |[설정: 지식 기반 보안 질문](#knowledge-based-security-questions) |
| 필요한 보안 질문 수를 변경하려면 어떻게 해야 하나요? |[설정: 등록 또는 재설정을 위한 보안 질문 수](#number-of-questions-required-to-register) |
| 로그인 시 사용자가 등록하도록 강제 적용하려면 어떻게 해야 하나요? |[암호 재설정의 강제 적용 등록 기반 롤아웃](#require-users-to-register-when-signing-in) |
| 사용자가 등록을 주기적으로 다시 확인하도록 강제 적용하려면 어떻게 해야 하나요? |[설정: 사용자가 해당 인증 데이터를 다시 확인해야 하기 전의 일 수](#number-of-days-before-users-must-confirm-their-contact-data) |
| 사용자가 관리자와 연락하는 방법을 사용자 지정하려면 어떻게 해야 하나요? |[설정: "관리자에게 문의" 링크 사용자 지정](#customize-the-contact-your-administrator-link) |
| 사용자가 암호를 재설정하지 않고 AD 계정의 잠금을 해제할 수 있도록 하려면 어떻게 해야 하나요? |[설정: 사용자가 암호를 재설정하지 않고 해당 AD 계정의 잠금을 해제하도록 설정](#allow-users-to-unlock-accounts-without-resetting-their-password) |
| 사용자에 대한 암호 재설정 알림을 사용하려면 어떻게 해야 하나요? |[설정: 암호가 재설정된 경우 사용자에게 알림](#notify-users-and-admins-when-their-own-password-has-been-reset) |
| 관리자에 대한 암호 재설정 알림을 사용하려면 어떻게 해야 하나요? |[설정: 관리자가 자신의 암호를 재설정하는 경우 다른 관리자에게 알림](#notify-admins-when-other-admins-reset-their-own-passwords) |
| 암호 재설정 모양과 느낌을 사용자 지정하려면 어떻게 해야 하나요? |[설정: 회사 이름, 브랜딩 및 로고 ](#password-management-look-and-feel) |

## <a name="password-management-look-and-feel"></a>암호 관리의 모양 및 느낌
다음 테이블에서 각 컨트롤이 암호 재설정을 위해 등록하여 암호를 재설정하는 사용자의 경험에 미치는 영향을 설명합니다.  [Azure 관리 포털](https://manage.windowsazure.com) 내 사용자의 **구성** 탭의 **디렉터리 속성** 섹션에서 이 옵션을 구성할 수 있습니다.

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>정책 제어</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>설명</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>영향을 줍니까?</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="directory-name">
                  <p>디렉터리 이름</p>
                </div>
              </td>
              <td>
                <p>조직 이름 사용자 또는 관리자가 암호 재설정 전자 메일 통신에서 확인할 수 있는 내용을 결정합니다.</p>
              </td>
              <td>
                <p>
                  <strong>"관리자에게 문의" 전자 메일:</strong>
                </p>
                <ul>
                  <li class="unordered">
친숙한 이름의 주소로 결정합니다. 예: "<strong>Wingtip Toys</strong> 대신 Microsoft"<br><br></li>
                  <li class="unordered">
전자 메일의 제목 결정 예: "<strong>Wingtip Toys</strong> 계정 전자 메일 확인 코드"<br><br></li>
                </ul>
                <p>
                  <strong>암호 재설정 전자 메일:</strong>
                </p>
                <ul>
                  <li class="unordered">
친숙한 이름의 주소로 결정합니다. 예: "<strong>Wingtip Toys</strong> 대신 Microsoft"<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="sign-in-and-access-panel-page-appearance">
                  <p>로그인 및 액세스 패널 페이지 모양</p>
                </div>
              </td>
              <td>
                <p>암호 재설정 페이지를 방문하는 사용자가 Microsoft 로고 또는 사용자 고유의 사용자 지정 로고를 참조하는 경우를 결정합니다.  이 구성 항목은 사용자 브랜드를 액세스 패널 및 로그인 페이지에도 추가합니다.</p>
                <p>

                </p>
                <p><a href="https://technet.microsoft.com/library/dn532270.aspx">회사 브랜드를 사용자 로그인 및 액세스 패널 페이지에 추가</a>에서 테넌트 브랜드 및 사용자 지정 기능에 대해 자세히 알아볼 수 있습니다.</p>
              </td>
              <td>
                <p>
                  <strong>암호 재설정 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
기본 Microsoft 로고 대신 암호 재설정 포털의 위쪽에 사용자의 로고를 표시할지 여부를 결정합니다.<br><br></li>
                  <li class="unordered">
                    <strong>참고:</strong> 직접 암호 재설정 페이지에 제공하는 경우 암호 재설정 포털의 첫 페이지에 로고가 보이지 않을 수도 있습니다.  사용자가 자신의 사용자 ID를 입력하고 다음을 클릭하면 로고가 표시됩니다.  다음과 같이 Whr 매개 변수를 암호 재설정 페이지로 전달하여 페이지 로드 시 로고를 강제로 나타나게 할 수 있습니다: <a href="https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com">https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com</a><br><br></li>
                </ul>
                <p>
                  <strong>"관리자에게 문의" 전자 메일:</strong>
                </p>
                <ul>
                  <li class="unordered">
사용자가 암호 재설정 UI에서 "관리자에 게 문의" 링크를 클릭하여 사용자에게 연락하도록 선택할 때 관리자에게 보낸 전자 메일의 아래쪽에 로고를 표시할지 여부를 결정합니다.<br><br></li>
                </ul>
                <p>
                  <strong>암호 재설정 전자 메일:</strong>
                </p>
                <ul>
                  <li class="unordered">
암호를 재설정할 때 사용자에게 보낸 전자 메일의 아래쪽에 로고를 표시할지 여부를 결정합니다.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## <a name="password-management-behavior"></a>암호 관리 동작 
다음 테이블에서 각 컨트롤이 암호 재설정을 위해 등록하여 암호를 재설정하는 사용자의 경험에 미치는 영향을 설명합니다.  [Azure 관리 포털](https://manage.windowsazure.com) 내 사용자 디렉터리의 **구성** 탭의 **사용자 암호 재설정 정책** 섹션에서 이 옵션을 구성할 수 있습니다.

> [!NOTE]
> 사용 중인 관리자 계정에는 이 정책 제어를 참조하기 위해 할당된 AAD Premium 라이선스가 있어야 합니다.<br><br>이 정책 제어는 관리자가 아닌 암호를 재설정하는 최종 사용자에게만 적용됩니다.  **관리자에게는 대체 전자 메일 및/또는 휴대폰을 변경할 수 없는 Microsoft에서 지정한 기본 정책이 있습니다.**
> 
> 

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>정책 제어</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>설명</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>영향을 줍니까?</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="users-enabled-for-password-reset">
                  <p>암호 재설정을 위해 사용할 수 있는 사용자</p>
                </div>
              </td>
              <td>
                <p>이 디렉터리의 사용자에 대한 암호 재설정을 사용할 수 있는지를 결정합니다. </p>
              </td>
              <td>
                <p>
                  <strong>등록 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
no로 설정된 경우, 사용자는 고유한 챌린지 데이터를 등록할 수 없습니다.<br><br></li>
                  <li class="unordered">
yes로 설정된 경우, 디렉터리의 모든 최종 사용자는 <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>의 등록 포털로 이동하여 챌린지 데이터를 등록할 수 있습니다.<br><br></li>
                  <li class="unordered">
                    <strong>참고:</strong> 사용자는 Azure AD Premium 또는 Basic 라이선스가 할당되어 있어야 암호 재설정을 위해 등록할 수 있습니다.<br><br></li>
                </ul>
                <p>
                  <strong>암호 재설정 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
no로 설정된 경우, 사용자는 암호를 재설정하려면 관리자에게 문의해야 한다는 메시지를 봅니다.<br><br></li>
                  <li class="unordered">
yes로 설정된 경우, 사용자는 <a href="http://passwordreset.microsoftonline.com">http://passwordreset.microsoftonline.com</a>으로 이동하거나 모든 조직 ID 로그인 페이지의 <strong>계정에 액세스할 수 없음</strong> 링크를 클릭하여 자동으로 암호를 재설정할 수 있습니다.<br><br></li>
                  <li class="unordered">
                    <strong>참고:</strong> 사용자는 Azure AD Premium 또는 Basic 라이선스가 할당되어 있어야 암호를 재설정할 수 있습니다.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="restrict-access-to-password-reset">
                  <p>암호 재설정에 대한 액세스 제한</p>
                </div>
              </td>
              <td>
                <p>특정 사용자 그룹만 암호 재설정을 사용하도록 허용되는지 여부를 결정합니다. (<strong>사용자 암호 재설정 활성화됨</strong>이 <strong>예</strong>로 설정된 경우에만 표시).</p>
              </td>
              <td>
                <p>
                  <strong>등록 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
이 설정은 암호 재설정 등록 포털에 대한 사용자의 액세스 권한에 영향을 주지 않습니다. <strong>사용자 암호 재설정 활성화됨</strong>이 <strong>예</strong>로 설정된 경우, 디렉터리의 모든 최종 사용자는 암호 재설정을 위해 <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>에서 등록할 수 있습니다.
                  </li>
                </ul>
                <p>
                  <strong>암호 재설정 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
no로 설정된 경우, 사용자 디렉터리의 모든 최종 사용자는 암호를 재설정할 수 있습니다.<br><br></li>
                  <li class="unordered">
예로 설정된 경우, <strong>암호 재설정을 수행할 수 있는 그룹</strong> 컨트롤에서 지정된 최종 사용자만이 암호를 재설정할 수 있습니다.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="group-that-can-perform-password-reset">
                  <p>암호 재설정을 수행할 수 있는 그룹</p>
                </div>
              </td>
              <td>
                <p>암호 재설정을 사용할 수 있는 최종 사용자의 그룹을 결정합니다. </p>
                <p>

                </p>
                <p>(<strong>암호 재설정에 대한 액세스 제한</strong>이 <strong>예</strong>로 설정된 경우에만 표시).</p>
              </td>
              <td>
                <p>
                  <strong>참고:</strong>
                </p>
                <ul>
                  <li class="unordered">
지정된 그룹이 없고 <strong>저장</strong>을 클릭한 경우, <strong>SSPRSecurityGroupUsers</strong>라는 빈 그룹이 생성됩니다.<br><br></li>
                  <li class="unordered">
자신의 그룹을 지정하려는 경우 고유한 표시 이름을 제공할 수 있습니다.<br><br></li>
                </ul>
                <p>
                  <strong>등록 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
이 설정은 암호 재설정 등록 포털에 대한 사용자의 액세스 권한에 영향을 주지 않습니다. <strong>사용자 암호 재설정 활성화됨</strong>이 <strong>예</strong>로 설정된 경우, 디렉터리의 모든 최종 사용자는 암호 재설정을 위해 <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>에서 등록할 수 있습니다.
                  </li>
                </ul>
                <p>
                  <strong>암호 재설정 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
<strong>암호 재설정에 대한 액세스 제한</strong>이 <strong>예</strong>로 설정된 경우, 이 그룹의 최종 사용자만이 암호를 재설정할 수 있습니다.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="authentication-methods-available-to-users">
                  <p>사용자에게 제공하는 인증 방법</p>
                </div>
              </td>
              <td>
                <p>사용자가 자신의 암호를 재설정 하는데 사용할 수 있는 챌린지를 결정합니다.</p>
                <p>

                </p>
                <p>(<strong>사용자 암호 재설정 활성화됨</strong>이 <strong>예</strong>로 설정된 경우에만 표시).</p>
              </td>
              <td>
                <p>
                  <strong>참고:</strong>
                </p>
                <ul>
                  <li class="unordered">
적어도 하나의 옵션을 선택해야 합니다.<br><br></li>
                  <li class="unordered">
암호를 재설정할 때 사용자에게 최고의 유연성을 제공하는 2 개 이상의 옵션을 사용하는 것이 좋습니다.<br><br></li>
                  <li class="unordered">
보안 질문을 사용하는 경우, 보안 질문은 전화 또는 전자 메일 기반 암호 재설정 방법보다 덜 안전할 수 있으므로 다른 인증 방법과 함께 사용하는 것이 좋습니다.<br><br></li>
                </ul>
                <p>
                  <strong>어떤 디렉터리 필드를 사용하고 있습니까?</strong>
                </p>
                <ul>
                  <li class="unordered">
사무실 전화는 디렉터리에서 사용자 개체에 대한 <strong>사무실 전화</strong> 특성에 해당합니다.<br><br></li>
                  <li class="unordered">
휴대폰은 <strong>인증 모바일</strong> 특성(공개적으로는 표시되지 않음) 또는 디렉터리의 사용자 개체에서 <strong>휴대폰</strong> 특성(공개적으로 표시됨)에 해당합니다.  서비스는 먼저 데이터에 대한 <strong>인증 전화</strong>을 확인하고, 없을 경우 <strong>휴대폰</strong> 특성으로 대체됩니다.<br><br></li>
                  <li class="unordered">
대체 전자 메일 주소는 <strong>인증 전자 메일</strong> 특성(공개적으로는 표시되지 않음) 또는 디렉터리의 사용자 개체에서 <strong>대체 전자 메일</strong> 특성(공개적으로 표시됨)에 해당합니다.  서비스는 먼저 데이터에 대한 <strong>인증 전자 메일</strong>을 확인하고, 없을 경우 <strong>대체 전자 메일</strong> 특성으로 대체됩니다.<br><br></li>
                  <li class="unordered">
보안 질문은 디렉터리에서 사용자 개체에 대해 비공개적으로 안전하게 저장되며 등록하는 동안 사용자만이 답변할 수 있습니다.  현재 보안을 위해 관리자가 이 대답을 편집하거나 볼 수 있는 방법은 없습니다.<br><br></li>
                  <li class="unordered">
                    <strong>참고: </strong>기본적으로 클라우드 특성 사무실 전화 및 휴대폰은 온-프레미스 디렉터리에서 클라우드 디렉터리에 동기화됩니다.  클라우드로 동기화되는 온-프레미스 특성에 대한 자세한 내용을 보려면, <a href="https://msdn.microsoft.com/library/azure/dn764938.aspx">Azure AD로 동기화된 특성을 참조하세요.</a><br><br></li>
                </ul>
                <p>
                  <strong>등록 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
사용자를 등록할 때 표시되는 인증 방법에 영향을 줍니다.  지정된 인증 메서드를 사용하지 않으면 사용자가 해당 인증 방법에 대해 자동 등록할 수 없습니다.<br><br></li>
                  <li class="unordered">
                    <strong>참고: </strong>현재 사용자는 자신의 사무실 전화번호를 등록할 수 없습니다. 관리자가 해당 인증 방법을 정의해야 합니다.<br><br></li>
                </ul>
                <p>
                  <strong>암호 재설정 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
사용자가 주어진 확인 단계에 대한 인증으로 사용할 수 있는 인증 방법을 결정합니다.  예를 들어, 사용자의 Azure Active Directory에 <strong>사무실 전화</strong> 및 <strong>인증 전화</strong> 필드에 데이터가 있는 경우, 이 두 인증 방법 중 하나를 사용하여 자신의 암호를 복구할 수 있습니다.<br><br></li>
                  <li class="unordered">
                    <strong>참고: </strong>사용자는 관리자 권한으로 사용하는 인증 방법으로 데이터를 표시하는 경우에만 자신의 암호를 재설정할 수 있습니다.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-authentication-methods-required">
                  <p>필수 인증 방법의 수</p>
                </div>
              </td>
              <td>
                <p>사용자가 자신의 암호를 다시 설정하기 위해 통과해야 사용 가능한 인증 방법의 최소 수를 결정합니다.</p>
                <p>

                </p>
                <p>(<strong>사용자 암호 재설정 활성화됨</strong>이 <strong>예</strong>로 설정된 경우에만 표시).</p>
              </td>
              <td>
                <p>
                  <strong>참고:</strong>
                </p>
                <ul>
                  <li class="unordered">
1 또는 2개의 필수 인증 방법을 설정할 수 있습니다.<br><br></li>
                </ul>
                <p>
                  <strong>등록 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
사용자가 등록해야 하는 최소 인증 방법을 결정해야 등록 과정을 완료할 수 있습니다.<br><br></li>
                </ul>
                <p>
                  <strong>암호 재설정 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
암호를 재설정하기 전에 사용자가 거쳐야 하는 확인 단계 수에 영향을 줍니다.  확인 단계는 인증 정보(해당 사무실 전화에 대한 호출과 같은)를 사용하여 사용자를 정의하거나 대체 전자 메일로 메일을 전송하여 자신의 계정을 확인합니다.<br><br></li>
                  <li class="unordered">
                    <strong>참고:</strong> 사용자는 설정한 정책에 따라 자신의 암호를 다시 설정하기 위해 자신의 계정에 정의된 필요한 인증 정보가 없는 경우, 관리자가 암호를 다시 설정하도록 요청하는 오류 페이지가 표시됩니다.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-questions-required-to-register">
                  <p>등록에 필요한 질문 수</p>
                </div>
              </td>
              <td>
                <p>보안 질문 옵션을 등록하는 경우 사용자가 답변해야하는 질문의 최소 수를 결정합니다.</p>
                <p>(<strong>보안 질문</strong> 확인란이 선택된 경우에만 표시됨).</p>
              </td>
              <td>
                <p>
                  <strong>참고:</strong>
                </p>
                <ul>
                  <li class="unordered">
등록에 필요한 3-5개의 질문으로 설정할 수 있습니다.<br><br></li>
                  <li class="unordered">
등록에 필요한 질문 수는 재설정에 필요한 질문 수보다 크거나 같아야 합니다.<br><br></li>
                  <li class="unordered">
사용자가 암호를 재설정할 때 더 많은 유연성을 가지고 있으므로 재설정에 필요한 수보다 더 높은 것으로 등록에 필요한 질문 수를 설정하는 것이 좋습니다.  등록한 질문의 모든 풀에서 응답하도록 사용자에 대한 질문을 임의로 선택하기 때문에 더 안전한 구성이기도 합니다.<br><br></li>
                </ul>
                <p>
                  <strong>등록 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
사용자가 암호 재설정을 위해 완전하게 등록되었다고 고려되기 전에 사용자가 답변해야하는 질문의 최소 수를 결정합니다.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-questions-required-to-reset">
                  <p>재설정에 필요한 질문 수 </p>
                </div>
              </td>
              <td>
                <p>암호를 재설정할 때 사용자가 답변해야하는 질문의 최소 수를 결정합니다.</p>
                <p>

                </p>
                <p>(<strong>보안 질문</strong> 확인란이 선택된 경우에만 표시됨).</p>
              </td>
              <td>
                <p>
                  <strong>참고:</strong>
                </p>
                <ul>
                  <li class="unordered">
재설정에 필요한 3-5개의 질문으로 설정할 수 있습니다.<br><br></li>
                  <li class="unordered">
재설정에 필요한 질문 수는 등록에 필요한 질문 수보다 적거나 같아야 합니다.<br><br></li>
                </ul>
                <p>
                  <strong>암호 재설정 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
사용자의 암호를 재설정할 때 사용자가 답변해야하는 질문의 최소 수를 결정합니다.<br><br></li>
                  <li class="unordered">
암호 재설정 시 사용자가 등록한 질문의 전체 목록에서 이 질문 수가 임의로 선택됩니다.  예를 들어, 사용자가 5개의 질문을 등록한 경우 사용자가 암호를 재설정하기 위해 작업하면 5개 중 3개의 질문이 임의로 선택됩니다.  사용자가 이들 질문에 모두 올바르게 답변해야 암호를 재설정할 수 있습니다.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="knowledge-based-security-questions">
                  <p>지식에 기반한 보안 질문</p>
                </div>
              </td>
              <td>
                <p>암호 재설정을 위해 등록할 때와 암호를 재설정할 때 사용자가 선택할 수 있는 미리 만든 보안 질문을 정의합니다.</p>
                <p>

                </p>
                <p>(<strong>보안 질문</strong> 확인란이 선택된 경우에만 표시됨).</p>
              </td>
              <td>
                <p>
                  <strong>참고:</strong>
                </p>
                <ul>
                  <li class="unordered">
모든 기술 기반 질문이 사용자의 브라우저 로캘을 기반으로 O365 언어의 전체 집합에 지역화될 예정입니다.<br><br></li>
                  <li class="unordered">
최대 총 20가지 질문이 정의될 수 있습니다.(사용자 지정 및 기술 기반 질문의 합)<br><br></li>
                 <li class="unordered">
최소 질문 문자 수 제한은 3자입니다.<br><br></li>
                  <li class="unordered">
최대 질문 문자 수 제한은 40자입니다.<br><br></li>
                  <li class="unordered">
사용자는 같은 질문에 두 번 답변하지 않을 수 있습니다.<br><br></li>
                  <li class="unordered">
사용자는 서로 다른 두 질문에 대해 동일한 대답을 제공할 수 없습니다.<br><br></li>
                  <li class="unordered">
대답을 정의하는데 모든 문자 집합을 사용할 수 있습니다(유니코드 문자 포함).<br><br></li>
                  <li class="unordered">
정의된 질문 수는 등록에 필요한 질문 수보다 크거나 같아야 합니다.<br><br></li>
                </ul>
                <p>
                  <strong>등록 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
암호 재설정을 위해 등록하는 경우 사용자가 답변을 제공할 수 있는 질문을 결정합니다.<br><br></li>
                </ul>
                <p>
                  <strong>암호 재설정 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
사용자가 암호를 재설정하는 데 사용할 수 있는 질문을 결정합니다.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="custom-security-questions">
                  <p>사용자 지정 보안 질문</p>
                </div>
              </td>
              <td>
                <p>암호 재설정을 위해 등록할 때와 암호를 재설정할 때 사용자가 선택할 수 있는 보안 질문을 정의합니다.</p>
                <p>

                </p>
                <p>(<strong>보안 질문</strong> 확인란이 선택된 경우에만 표시됨).</p>
              </td>
              <td>
                <p>
                  <strong>참고:</strong>
                </p>
                <ul>
                  <li class="unordered">
최대 총 20가지 질문이 정의될 수 있습니다.(사용자 지정 및 기술 기반 질문의 합)<br><br></li>
                  <li class="unordered">
최대 질문 문자 수 제한은 200자입니다.<br><br></li>
                  <li class="unordered">
최소 질문 문자 수 제한은 3자입니다.<br><br></li>
                  <li class="unordered">
최대 질문 문자 수 제한은 40자입니다.<br><br></li>
                  <li class="unordered">
사용자는 같은 질문에 두 번 답변하지 않을 수 있습니다.<br><br></li>
                  <li class="unordered">
사용자는 서로 다른 두 질문에 대해 동일한 대답을 제공할 수 없습니다.<br><br></li>
                  <li class="unordered">
질문 및 대답을 정의하는데 모든 문자 집합을 사용할 수 있습니다.(유니코드 문자 포함)<br><br></li>
                  <li class="unordered">
정의된 질문 수는 등록에 필요한 질문 수보다 크거나 같아야 합니다.<br><br></li>
                  <li class="unordered">
서로 다른 로캘에 대한 다양한 질문은 사용자 지정 질문에 아직 지원되지 않습니다.  사용자의 브라우저 로캘이 다른 경우 모든 사용자 지정 질문을 관리 UI에 입력할 수 있는 언어에 표시합니다.  이러한 질문을 지역화할 필요한 경우 대신 "지식 기반" 질문을 사용합니다.<br><br></li>
                </ul>
                <p>
                  <strong>등록 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
암호 재설정을 위해 등록하는 경우 사용자가 답변을 제공할 수 있는 질문을 결정합니다.<br><br></li>
                </ul>
                <p>
                  <strong>암호 재설정 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
사용자가 암호를 재설정하는 데 사용할 수 있는 질문을 결정합니다.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="require-users-to-register-when-signing-in">
                  <p>로그인 시 사용자가 등록해야 하나요?</p>
                </div>
                <p>

                </p>
              </td>
              <td>
                <p>다음에 로그인할 때 사용자가 암호 재설정을 위해 연락처 데이터를 등록해야 하는 경우를 결정합니다.  
                </p>
                <p>이 기능은 회사 또는 학교 계정을 사용하는 모든 로그인 페이지에서 작동합니다.  이러한 페이지에는 모든 Office 365, Azure 관리 포털, 액세스 패널 및 로그인에 Azure AD를 사용하는 모든 페더레이션 또는 사용자 지정 개발 응용 프로그램이 포함됩니다.
                </p>
                <p>

                </p>
                <p>강제 적용 등록은 암호 재설정을 사용할 수 있는 사용자에게만 적용됩니다. 따라서 "암호 재설정에 대한 액세스 제한" 기능 및 특정 사용자 그룹으로 범위가 지정된 암호 재설정을 사용한 경우 로그인 시 해당 그룹 사용자만 암호 재설정을 등록해야 합니다.</p>
                <p>

                </p>
                <p>(<strong>사용자 암호 재설정 활성화됨</strong>이 <strong>예</strong>로 설정된 경우에만 표시).</p>
              </td>
              <td>
                <p>

                </p>
                <p>

                </p>
                <p>
                  <strong>참고:</strong>
                </p>
                <ul>
                  <li class="unordered">
이 기능을 사용하지 않도록 설정하면 수동으로 사용자를 <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>으로 보내 연락 데이터를 등록할 수 있습니다.  <br><br></li>
                  <li class="unordered">
사용자가 액세스 패널의 프로필 탭에서 <strong>암호 재설정을 위해 등록</strong> 링크를 클릭하여 등록 포털에 접근할 수도 있습니다.<br><br></li>
                  <li class="unordered">
취소 단추를 클릭하거나 창을 닫어 이 메서드를 통한 등록을 해제할 수 있지만 등록하지 않은 경우 로그인할 때마다 사용자에게 알려줍니다.<br><br></li>
                </ul>
                <p>
                  <strong>등록 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
이 설정은 등록 포털 자체의 동작에 영향을 주지 않으며, 대신 액세스 패널에 로그인할 때 등록 포털을 사용자에게 표시할지 여부를 결정합니다.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-days-before-users-must-confirm-their-contact-data">
                  <p>사용자가 연락 데이터를 확인하기 전까지 남은 일 수</p>
                </div>
              </td>
              <td>
                <p><strong>사용자가 로그인 시 등록하도록 요구</strong>가 꺼져 있으면, 이 설정은 사용자가 데이터를 다시 확인해야 하기 전에 경과할 수 있는 기간을 결정합니다. </p>
                <p>

                </p>
                <p>(<strong>사용자가 액세스 패널에 로그인할 때 등록</strong>이 <strong>예</strong>로 설정된 경우에만 표시됨).</p>
              </td>
              <td>
                <p>

                </p>
                <p>

                </p>
                <p>
                  <strong>참고: </strong>
                </p>
                <ul>
                  <li class="unordered">
0 - 730일 사이의 값이 허용되며, 0일은 사용자가 연락처 데이터를 다시 확인하도록 요청받지 않음을 의미합니다.<br><br></li>
                </ul>
                <p>
                  <strong>등록 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
이 설정은 등록 포털 자체의 동작에 영향을 주지 않으며, 대신 연락처 데이터를 다시 확인해야 할 때 등록 포털을 사용자에게 표시할지 여부를 결정합니다. <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="customize-the-contact-your-administrator-link">
                  <p>관리자가 링크하는 연락처를 사용자 지정합니까?</p>
                </div>
              </td>
              <td>
                <p>사용자가 사용자 지정 URL 또는 전자 메일 주소에 대한 작업 지점에 너무 오래 대기하거나 오류가 발생한 경우, 암호 재설정 포털에 관리자가 링크하는(왼쪽에 표시된) 연락처를 표시할 지 여부를 제어합니다.</p>
                <p>

                </p>
                <p>(<strong>사용자 암호 재설정 활성화됨</strong>이 <strong>예</strong>로 설정된 경우에만 표시).</p>
              </td>
              <td>
                <p>
                  <strong>참고: </strong>
                </p>
                <ul>
                  <li class="unordered">
이 설정을 사용하는 경우, 이 설정 바로 아래의 <strong>사용자 지정 전자 메일 주소 또는 URL</strong> 필드를 채워 사용자 지정 URL 또는 전자 메일 주소를 선택해야 합니다.<br><br></li>
                </ul>
                <p>
                  <strong>암호 재설정 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
아니오로 설정된 경우, 사용자가 강조 표시된 링크를 클릭하면 암호 재설정을 요청하는 모든 테넌트 관리자의 기본 전자 메일 주소로 전자 메일을 발송합니다.  이 전자 메일은 아래 논리에 따라 디스패치됩니다.<br><br></li>
                  <li class="unordered">
                    <ul>
                      <li class="unordered">
암호 관리자가 있는 경우 최대 100개의 전체 받는 사람까지 모든 암호 관리자에게 전자 메일을 보냅니다.<br><br></li>
                      <li class="unordered">
암호 관리자가 없는 경우 최대 100개의 전체 받는 사람까지 모든 관리자에게 전자 메일을 보냅니다.<br><br></li>
                      <li class="unordered">
사용자 관리자가 없는 경우 최대 100개의 전체 받는 사람까지 모든 전역 관리자에게 전자 메일을 보냅니다.<br><br></li>
                    </ul>
                  </li>
                  <li class="unordered">
예로 설정된 경우, 이 설정은 위의 강조표시된 링크의 동작을 사용자 지정하여, 사용자가 암호 재설정에 도움을 받기 위해 이동할 수 있는 사용자 지정 URL 또는 전자 메일 주소를 가리킵니다.<br><br></li>
                  <li class="unordered">
URL을 지정하는 경우 새 탭에서 열립니다.<br><br></li>
                  <li class="unordered">
전자 메일 주소를 지정하면 해당 전자 메일 주소로 mailto 링크를 만듭니다.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="custom-email-address-or-URL">
                  <p>사용자 지정 전자 메일 주소 또는 URL</p>
                </div>
              </td>
              <td>
                <p><strong>관리자에게 문의</strong> 링크가 가리키는 전자 메일 주소 또는 URL을 제어합니다. </p>
                <p>

                </p>
                <p>(<strong>관리자에게 문의 링크 사용자 지정 여부</strong>가 <strong>예</strong>로 설정된 경우에만 표시).</p>
              </td>
              <td>
                <p>
                  <strong>참고:</strong>
                </p>
                <ul>
                  <li class="unordered">
유효한 인트라넷 또는 엑스트라넷 URL 또는 전자 메일 주소여야 합니다.<br><br></li>
                </ul>
                <p>
                  <strong>암호 재설정 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
<strong>관리자에게 문의</strong> 링크가 가리키는 위치를 변경합니다.<br><br></li>
                  <li class="unordered">
전자 메일 주소를 제공하는 경우 해당 전자 메일 주소에 대한 "mailto" 링크가 됩니다.<br><br></li>
                  <li class="unordered">
URL을 제공하는 경우, 해당 링크는 새 탭에서 열리는 해당 URL을 가리키는 표준 href가 됩니다.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="write-back-passwords-to-on-premises-directory">
                  <p>온-프레미스 디렉터리에 대한 암호 쓰기 저장</p>
                </div>
              </td>
              <td>
                <p>이 디렉터리에 대해 암호 쓰기 저장을 사용할 것인 지 여부를 제어하고, 쓰기 저장이 켜 있는 경우 온-프레미스 쓰기 저장 서비스의 상태를 나타냅니다.</p>
                <p>

                </p>
                <p>이 설정은 Azure AD 연결을 다시 구성하지 않고 서비스를 일시적으로 사용하지 않도록 설정하려는 경우에 유용합니다.</p>
              </td>
              <td>
                <p>

                </p>
                <p>
                  <strong>참고:</strong>
                </p>
                <ul>
                  <li class="unordered">
Azure AD Connect의 최신 버전을 다운로드 하고 <strong>선택적 기능</strong> 선택 화면에서 <strong>비밀번호 쓰기 저장</strong> 옵션을 사용하도록 설정하여 비밀번호 쓰기 저장을 설치한 경우에만 이 컨트롤이 표시됩니다.<br><br></li>
                  <li class="unordered">
비밀번호 쓰기 저장을 사용하도록 설정하고 서비스에 구성 문제가 있다고 생각하는 경우, 이 탭으로 이동하고 잘못된 경우 <strong>비밀번호 쓰기 저장 서비스 상태</strong> 레이블을 살펴봅니다.<br><br></li>
                  <li class="unordered">
표시할 수 있는 상태는 다음과 같습니다.<br><br><ul><li class="unordered"><strong>구성됨 </strong>– 모든 것이 예상대로 작동됩니다.<br><br></li><li class="unordered"><strong>구성되지 않음</strong> – 쓰기 저장은 설치되어 있지만 서비스에 도달할 수 없어, 443에 대한 아웃 바운드 연결을 차단하지 않았는지 확인하고 여전히 문제가 있을 경우 서비스를 다시 설치합니다.<br><br></li></ul></li>
                </ul>
                <p>
                  <strong>등록 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
쓰기 저장이 배포 및 구성되며 이 스위치가 <strong>아니요</strong>로 설정된 경우, 쓰기 저장은 사용하지 않도록 설정되며 페더레이션 및 암호 해시 동기화된 사용자는 암호를 재설정하는 암호를 등록할 수 없습니다.<br><br></li>
                  <li class="unordered">
스위치가 <strong>예</strong>로 설정된 경우, 쓰기 저장은 사용하도록 설정되며 페더레이션 및 암호 해시 동기화된 사용자는 암호를 재설정할 수 있습니다.<br><br></li>
                </ul>
                <p>
                  <strong>암호 재설정 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
쓰기 저장이 배포 및 구성되며 이 스위치가 <strong>아니요</strong>로 설정된 경우, 쓰기 저장은 사용하지 않도록 설정되며 페더레이션 및 암호 해시 동기화된 사용자는 암호를 재설정할 수 없습니다.<br><br></li>
                  <li class="unordered">
스위치가 <strong>예</strong>로 설정된 경우, 쓰기 저장은 사용하도록 설정되며 페더레이션 및 암호 해시 동기화된 사용자는 암호를 재설정할 수 있습니다.<br><br></li>
                </ul>
              </td>
            </tr>
             <tr>
              <td>
                <div id="allow-users-to-unlock-accounts-without-resetting-their-password">
                  <p>사용자가 해당 암호를 재설정하지 않고 계정의 잠금을 해제할 수 있음</p>
                </div>
              </td>
              <td>

              <p>암호 재설정 포털을 방문하는 사용자에게 해당 암호를 재설정하지 않고 온-프레미스 Active Directory 계정의 잠금을 해제할 수 있는 옵션을 제공해야 하는지 여부를 지정합니다. 기본적으로 Azure AD는 암호 재설정을 수행할 때 항상 계정의 잠금을 해제하며 이 설정을 통해 이러한 두 작업을 분리할 수 있습니다.</p>

              <p>"예"로 설정하면 사용자에게 암호를 재설정하고 계정의 잠금을 해제하거나, 암호를 재설정하지 않고 잠금을 해제할 수 있는 옵션이 제공됩니다. </p>

              <p>“아니요”로 설정하면 사용자가 암호 재설정 및 계정 잠금 해제가 결합된 작업만 수행할 수 있습니다.</p>

              </td>
              <td>
                <p>
                  <strong>참고:</strong>
                </p>
                <ul>
                  <li class="unordered">
이 기능을 사용하려면 Azure AD Connect의 2015년 8월 이상 릴리스(v. 1.0.8667.0 이상)를 설치해야 합니다.<br><br><a href="http://www.microsoft.com/download/details.aspx?id=47594">여기를 클릭하여 최신 버전의 Azure AD Connect를 다운로드합니다.</a></li>

                  <li class="unordered">
                    <strong>참고:</strong> 이 기능을 테스트하려면 비밀번호 쓰기 저장을 사용하도록 설정해야 하며 온-프레미스 기반(예: 페더레이션 또는 암호 동기화된 사용자) 및 잠긴 계정이 있는 계정을 사용합니다.  온-프레미스 기반이 아니고 잠긴 계정이 없는 사용자에게는 해당 계정의 잠금을 해제할 수 있는 옵션이 표시되지 않습니다.</li>
                </ul>
                <p>
                  <strong>암호 재설정 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
이 옵션을 사용하도록 설정한 후, 잠긴 온-프레미스 계정이 있는 사용자가 암호 재설정 포털에 액세스하면 암호를 재설정하지 않고 해당 계정의 잠금을 해제할 수 있는 옵션이 제공됩니다.<br><br>비밀번호 쓰기 저장을 사용하는 경우 암호가 재설정될 때 계정이 이미 자동으로 잠금 해제됩니다. 이 옵션은 이러한 작업을 간단하게 분리합니다.<br><br>지원 센터에 오는 많은 전화가 계정 잠금 해제 요청인 경우 이 옵션을 사용하도록 설정하면 특히 유용합니다.</li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## <a name="password-management-notifications"></a>암호 관리 알림
다음 테이블에서 각 컨트롤이 암호 재설정 알림을 수신하는 사용자와 관리자의 경험에 미치는 영향을 설명합니다.  [Azure 관리 포털](https://manage.windowsazure.com) 내 사용자 디렉터리의 **구성** 탭의 **알림** 섹션에서 이 옵션을 구성할 수 있습니다.

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>정책 제어</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>설명</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>영향을 줍니까?</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="notify-admins-when-other-admins-reset-their-own-passwords">
                  <p>다른 관리자가 암호를 다시 설정했을 때 관리자에게 알림</p>
                </div>
              </td>
              <td>
                <p>모든 종류의 다른 관리자가 자신의 암호를 다시 설정하는 경우, 모든 전역 관리자가 자신의 기본 전자 메일 주소로 전자 메일을 통해 알림을 받을 지 여부를 결정합니다.</p>
              </td>
              <td>
                <p>
                  <strong>암호 재설정 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
아니오로 설정된 경우, 알림이 전송되지 않습니다.<br><br></li>
                  <li class="unordered">
예로 설정된 경우, 단일 관리자가 자신의 암호를 다시 설정하면 다른 모든 전역 관리자에게 알립니다.<br><br></li>
                  <li class="unordered">
이 알림은 조직의 다른 모든 전역 관리자의 기본 전자 메일 주소로 전자 메일을 통해 전송됩니다.<br><br></li>
                </ul>
                <p>
                  <strong>예:</strong>
                </p>
                <ul>
                  <li class="unordered">
이 옵션을 사용할 수 있으며 관리자 A가 자신의 암호를 다시 설정하면, 테넌트, B, C, D에 다른 3개의 관리자가 있고, 관리자 B, C, D는 관리자 A가 암호를 다시 설정했다는 전자 메일을 수신합니다.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="notify-users-and-admins-when-their-own-password-has-been-reset">
                  <p>암호가 다시 설정되었을 때 사용자 및 관리자에게 알림</p>
                </div>
              </td>
              <td>
                <p>자신의 암호를 다시 설정한 최종 사용자나 관리자가 자신의 암호를 재설정했다는 전자 메일 알림을 받게 됩니다.</p>
              </td>
              <td>
                <p>
                  <strong>암호 재설정 포털:</strong>
                </p>
                <ul>
                  <li class="unordered">
아니오로 설정된 경우, 알림이 전송되지 않습니다.<br><br></li>
                  <li class="unordered">
예로 설정된 경우, 사용자 또는 관리자가 자신의 암호를 다시 설정할 때마다 자신이 암호를 다시 설정했다는 알림을 받게 됩니다.<br><br></li>
                  <li class="unordered">
이 알림은 자신의 암호를 재설정한 사용자의 사용자 계정 이름 및 대체(또는 인증) 전자 메일 주소로 전자 메일을 통해 전송됩니다.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>암호 재설정 설명서에 대한 링크
다음은 모든 Azure AD 암호 재설정 설명서 페이지에 대한 링크입니다.

* **로그인하는 데 문제가 있나요?** 그렇다면 [암호를 변경하고 재설정하는 방법은 다음과 같습니다](active-directory-passwords-update-your-own-password.md).
* [**작동 방식**](active-directory-passwords-how-it-works.md) - 6개의 다양한 구성 요소 서비스 및 기능에 대해 알아봅니다.
* [**시작하기**](active-directory-passwords-getting-started.md) -사용자가 클라우드 또는 온-프레미스 암호를 다시 설정하고 변경할 수 있는 방법에 대해 알아봅니다.
* [**모범 사례**](active-directory-passwords-best-practices.md) - 사용자의 조직에서 신속하게 배포하고 효과적으로 암호를 관리하는 방법에 대해 알아봅니다.
* [**정보 활용**](active-directory-passwords-get-insights.md) -우리의 통합된 보고 기능에 대해 알아봅니다.
* [**FAQ**](active-directory-passwords-faq.md) -자주 묻는 질문에 답변합니다.
* [**문제 해결**](active-directory-passwords-troubleshoot.md) -신속하게 서비스의 문제를 해결하는 방법에 대해 알아봅니다.
* [**자세히 알아보기**](active-directory-passwords-learn-more.md) -서비스의 작동 원리 방식의 기술적 측면을 자세히 알아봅니다.

[001]: ./media/active-directory-passwords-customize/001.jpg "Image_001.jpg"



<!--HONumber=Nov16_HO3-->


