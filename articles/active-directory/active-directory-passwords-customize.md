---
title: "사용자 지정: Azure AD SSPR | Microsoft Docs"
description: 
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: joflore
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 41da786ed13308a1fc030e6b02fac3d8fbca9e61
ms.contentlocale: ko-kr
ms.lasthandoff: 05/03/2017


---
# <a name="customize-azure-ad-functionality-for-self-service-password-reset"></a>셀프 서비스 암호 재설정을 위한 Azure AD 기능 사용자 지정

Azure AD 내에서 변경될 수 있는 많은 옵션이 있고 이중 다수가 SSPR(셀프 서비스 암호 재설정)과 관련됩니다. 이 가이드에서는 가능한 기능을 설명합니다.

## <a name="customize-the-contact-your-administrator-link"></a>관리자에게 문의 링크 사용자 지정

SSPR을 사용하지 않는 경우라도 사용자는 암호 재설정 포털에서 "관리자에게 문의" 링크를 사용합니다.  이 링크를 클릭하면 사용자의 암호를 변경하는 데 도움을 요청하기 위해 관리자에게 전자 메일을 보내도록 연결합니다. 이 전자 메일은 다음 순서로 다음과 같은 받는 사람에게 전송됩니다.

1. **암호 관리자** 역할이 할당된 경우 이 역할이 있는 항목이 표시됩니다
2. 암호 관리자가 할당되지 않은 경우 **사용자 관리자** 역할이 있는 항목이 표시됩니다.
3. 이전의 역할이 할당되지 않은 경우 **전역 관리자**가 표시됩니다

어떠한 경우에도 최대 100개의 받는 사람이 표시됩니다.

### <a name="disable-contact-your-administrator-emails"></a>관리자에게 문의 전자 메일 사용 안 함

조직에서 관리자에게 암호 재설정 요청을 표시하지 않으려는 경우 다음 구성을 사용할 수 있습니다.

* 모든 최종 사용자에게 셀프 서비스 암호 재설정 사용 이 옵션은 **암호 재설정**, **속성** 아래에 있습니다.
    * 사용자가 자신의 암호를 재설정할 수 없게 하려는 경우 빈 그룹에 대한 액세스 범위를 지정하면 됩니다. 이 옵션은 사용하지 않는 것이 좋습니다**.
* 사용자가 도움을 받을 수 있는 웹 URL 또는 mailto: 주소를 제공하는 고객 지원 센터 링크를 사용자 지정합니다. 이 옵션은 **암호 재설정**, **사용자 지정**, **기술 지원팀 전자 메일 또는 URL 사용자 지정** 아래에 있습니다.

## <a name="customize-the-sign-in-and-access-panel-look-and-feel"></a>로그인 및 액세스 패널 모양과 느낌 사용자 지정

사용자가 로그인 페이지에 액세스할 때 로그인 페이지 이미지와 함께 표시되는 로고를 회사 브랜딩에 맞게 사용자 지정할 수 있습니다.

다음과 같은 경우에는 이러한 그래픽이 표시됩니다.

* 사용자가 사용자 이름을 입력한 후
* 사용자가 사용자 지정된 URL에 액세스
    * "Whr" 매개 변수를 "https://login.microsoftonline.com/?whr=contoso.com"과 같은 암호 재설정 페이지에 전달하는 방법
    * "username" 매개 변수를 "https://login.microsoftonline.com/?username=admin@contoso.com"과 같은 암호 재설정 페이지에 전달하는 방법

### <a name="graphics-details"></a>그래픽 정보

다음 설정을 사용하면 로그인 페이지의 시각적 특성을 변경할 수 있습니다. 이 설정은 **Azure Active Directory**, **회사 브랜딩**, **회사 브랜딩 편집**에서 확인할 수 있습니다.

* 로그인 페이지 이미지는 500KB 이하의 1420x1200픽셀인 JPG 또는 PNG 파일이어야 합니다. 최상의 결과를 위해 약 200KB 정도가 좋습니다.
* 로그인 페이지 배경색은 대기 시간이 긴 연결에서 작업하는 경우에 사용되고 RGB 16진수 형식이어야 합니다.
* 배너 이미지는 10KB 이하의 60x280픽셀인 JPG 또는 PNG 파일이어야 합니다.
* 정사각형 로고(일반 및 어두운 테마)는 10KB 이하의 240x240(크기 조정 가능)픽셀인 JPG 또는 PNG 파일입니다.

### <a name="sign-in-text-options"></a>로그인 텍스트 옵션

다음 설정을 사용하면 텍스트를 조직에 관련된 로그인 페이지에 추가할 수 있습니다. 이러한 설정은 **Azure Active Directory**, **회사 브랜딩**, **회사 브랜딩 편집**에서 찾을 수 있습니다.

* **사용자 이름 힌트**는 someone@example.com 텍스트의 예제를 사용자에게 보다 적합한 형태로 바꿉니다. 이 기능은 내부 및 외부 사용자를 지원할 때 기본으로 설정하는 것이 좋습니다.
* **로그인 페이지 텍스트**는 최대 256자입니다. 이 텍스트는 사용자가 온라인으로 로그인하는 경우 및 Windows 10의 Azure AD 조인 경험에서 표시됩니다. 사용 약관, 지침 및 사용자에 대한 팁에서 이 텍스트를 사용합니다. **누구든지 로그인 페이지를 볼 수 있으므로 여기에서 중요 정보를 제공하지 않도록 합니다.**

### <a name="keep-me-signed-in-disabled"></a>로그인 사용 안 함

"로그인 사용 안 함" 옵션을 사용하면 사용자가 해당 브라우저 창을 닫고 다시 열 때 로그인 상태를 유지할 수 있으며 세션 수명에 영향을 주지 않습니다. 이러한 설정은 **Azure Active Directory**, **회사 브랜딩**, **회사 브랜딩 편집**에 있습니다.

SharePoint Online과 Office 2010의 일부 기능은 이 확인란을 선택할 수 있는 사용자에 대한 종속성이 있습니다. 이 옵션을 숨기면 사용자에게 추가 및 예기치 않은 로그인 프롬프트가 표시될 수 있습니다.

### <a name="directory-name"></a>디렉터리 이름

**Azure Active Directory**, **속성** 아래에서 이름 특성을 변경하여 포털 및 자동화된 통신에서 친숙한 조직 이름을 표시할 수 있습니다. 이 옵션은 계속되는 양식에서 자동화된 전자 메일의 형태로 표시됩니다.

* "CONTOSO 데모를 대신하는 Microsoft" 전자 메일의 친숙한 이름
* "CONTOSO 데모 계정 전자 메일 확인 코드" 전자 메일의 제목 줄

## <a name="next-steps"></a>다음 단계

다음 링크는 Azure AD를 사용한 암호 재설정에 대한 추가 정보를 제공합니다.

* [**빠른 시작**](active-directory-passwords-getting-started.md) - Azure AD 셀프 서비스 암호 관리를 사용하여 운영 시작 
* [**라이선스**](active-directory-passwords-licensing.md) - Azure AD 라이선스 구성
* [**데이터**](active-directory-passwords-data.md) - 암호 관리에 필요한 데이터 및 사용 방식을 이해
* [**롤아웃**](active-directory-passwords-best-practices.md) - 여기서 제공하는 지침을 사용하여 배포 계획을 세우고 사용자에게 SSPR 배포
* [**정책**](active-directory-passwords-policy.md) - Azure AD 암호 정책을 이해하고 설정
* [**비밀번호 쓰기 저장**](active-directory-passwords-writeback.md) - 비밀번호 쓰기 저장이 온-프레미스 디렉터리와 함께 작동 하는 원리
* [**보고**](active-directory-passwords-reporting.md) - 사용자가 SSPR 기능에 액세스하는 조건, 시간 및 위치 탐색
* [**기술 심층 분석**](active-directory-passwords-how-it-works.md) - 작동 방식을 이해하기 위해 심층 분석
* [**질문과 대답**](active-directory-passwords-faq.md) - 어떤 방식으로? 그 이유는 무엇을? 어디서? 누가? 언제? - 많은 분들이 항상 묻는 질문에 대한 답변입니다.
* [**문제 해결**](active-directory-passwords-troubleshoot.md) - SSPR의 일반적인 문제 해결 방법 알아보기


