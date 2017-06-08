---
title: "빠른 시작: Azure AD SSPR | Microsoft 문서"
description: "신속하게 Azure AD 셀프 서비스 암호 재설정 배포"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: gahug
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/12/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 5a338029cea5f448fc4d84a1a9c01c867ecd5028
ms.contentlocale: ko-kr
ms.lasthandoff: 05/13/2017


---
# <a name="quick-start-azure-ad-self-service-password-reset"></a>빠른 시작: Azure AD 셀프 서비스 암호 재설정

## <a name="rapidly-deploy-self-service-password-reset"></a>신속하게 셀프 서비스 암호 재설정 배포

IT 관리자는 SSPR(셀프 서비스 암호 재설정)을 사용하여 사용자에게 자신의 암호 또는 계정을 재설정하거나 잠금 해제할 수 있는 권한을 간단하게 부여할 수 있습니다. 이 시스템에는 오용 또는 남용에 대해 경고하는 알림과 함께 사용자가 언제 시스템을 사용하는지 추적하는 구체적인 보고서가 포함되어 있습니다.

이 가이드에서는 이미 여러분이 작업 평가판 또는 사용이 허가된 Azure AD 테넌트를 갖고 있다고 가정합니다. Azure AD 설정과 관련하여 도움이 필요한 경우 [Azure AD 시작하기](https://azure.microsoft.com/trial/get-started-active-directory/) 문서를 참조하세요.

1. 기존 Azure AD 테넌트에서 **"암호 재설정"**을 선택합니다.

2. **"속성"** 화면의 "셀프 서비스 암호 재설정이 사용하도록 설정됨" 옵션에서 다음 중 하나를 선택합니다.
    * 아무도 없음 - 아무도 SSPR 기능을 사용할 수 없음
    * 그룹 - 사용자가 선택하는 특정 Azure AD 그룹 구성원만 SSPR 기능을 사용할 수 있음
    * 모든 사람 - Azure AD 테넌트에 계정이 있는 모든 사용자가 SSPR 기능을 사용할 수 있음

3. **"인증 방법"** 화면에서 다음을 선택합니다.
    * "재설정에 필요한 방법 수" - 최소 한 가지 또는 최대 두 가지를 지원합니다.
    * "사용자가 사용할 수 있는 방법" - 적어도 한 가지 이상이 필요하지만 그보다 많아도 상관 없습니다.
        * **전자 메일**은 사용자가 구성한 인증 전자 메일 주소로 코드가 포함된 전자 메일을 보냅니다.
        * **휴대폰**은 사용자가 구성한 휴대폰 번호로 코드가 포함된 전화 또는 문자 메시지 중에 무엇을 받을 것인지 선택할 수 있습니다.
        * **사무실 전화**는 사용자가 구성한 사무실 전화 번호로 코드가 포함된 전화를 겁니다.
        * **보안 질문**에서는 다음을 선택해야 합니다.
            * "등록에 필요한 질문 수"는 등록에 필요한 최소 수입니다. 즉, 사용자는 더 많은 질문에 답하도록 선택하여 질문을 가져올 질문 풀을 만들 수 있습니다. 이 옵션은 3-5로 설정해야 하며 재설정에 필요한 질문 수보다 많거나 같아야 합니다.
            * "재설정에 필요한 질문 수"는 3-5개 질문으로 설정할 수 있으며, 질문에 정확하게 대답해야만 사용자 암호를 재설정하거나 잠금 해제할 수 있습니다.
                * 사용자 지정 질문은 보안 질문을 선택할 때 "사용자 지정" 단추를 클릭하여 추가할 수 있습니다.

4. 권장 사항: **"사용자 지정"**을 사용하여 "관리자에게 문의" 링크가 사용자가 정의한 페이지 또는 전자 메일 주소를 가리키도록 변경할 수 있습니다

5. 선택 사항: **"등록"** 화면은 관리자에게 다음 옵션을 제공합니다.
    * "로그인 시 사용자가 등록하도록 요구"
    * "사용자가 인증 정보를 다시 확인해야 하기 전의 일 수"

6. 선택 사항: **"알림"** 화면은 관리자에게 다음 옵션을 제공합니다.
    * 사용자에게 암호 재설정에 대해 알림
    * 다른 관리자가 암호를 재설정하면 모든 관리자에게 알림

**현재는 Azure AD 테넌트에 SSPR을 구성했습니다**. 여기서 그만 해도 되고 계속해서 온-프레미스 AD 도메인에 암호 동기화를 구성해도 됩니다.

> [!NOTE]
> 관리자가 아닌 사용자로 SSPR을 테스트합니다. Microsoft가 Azure 관리자 유형 계정에 강력한 인증 요구 사항을 적용하기 때문입니다. 관리자 암호 정책에 대한 자세한 내용은 [암호 정책 문서](active-directory-passwords-policy.md#administrator-password-policy-differences)를 참조하세요.

## <a name="configure-synchronization-to-existing-identity-source"></a>기존 ID 소스에 동기화 구성

Azure AD에 온-프레미스 ID 동기화를 사용하려면 조직의 서버에 [Azure AD Connect](./connect/active-directory-aadconnect.md)를 설치하고 구성해야 합니다. 이 응용 프로그램은 기존 ID 소스에서 Azure AD 도메인으로의 사용자 및 그룹 동기화를 처리합니다.

* [DirSync 또는 Azure AD Sync에서 Azure AD Connect로 업그레이드](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [기본 설정을 사용하여 Azure AD Connect 시작](./connect/active-directory-aadconnect-get-started-express.md)
* [비밀번호 쓰기 저장을 구성](active-directory-passwords-writeback.md#configuring-password-writeback)하여 Azure AD에서 온-프레미스 디렉터리로 비밀번호 다시 쓰기

## <a name="disabling-self-service-password-reset"></a>셀프 서비스 암호 재설정 해제

셀프 서비스 암호 재설정을 해제하는 방법은 아주 간단합니다. Azure AD 테넌트를 열고, **암호 재설정**, **속성**으로 이동하여 **셀프 서비스 암호 재설정이 사용하도록 설정됨** 아래에서 **아무도 없음**을 선택하면 됩니다.

## <a name="next-steps"></a>다음 단계
다음 링크는 Azure AD를 사용한 암호 재설정에 대한 추가 정보를 제공합니다.

* [**라이선스**](active-directory-passwords-licensing.md) - Azure AD 라이선스 구성
* [**데이터**](active-directory-passwords-data.md) - 암호 관리에 필요한 데이터 및 사용 방식을 이해
* [**롤아웃**](active-directory-passwords-best-practices.md) - 여기서 제공하는 지침을 사용하여 배포 계획을 세우고 사용자에게 SSPR 배포
* [**사용자 지정**](active-directory-passwords-customize.md) - 회사 SSPR 경험의 모양과 느낌을 사용자 지정.
* [**정책**](active-directory-passwords-policy.md) - Azure AD 암호 정책을 이해하고 설정
* [**보고**](active-directory-passwords-reporting.md) - 사용자가 SSPR 기능에 액세스하는 조건, 시간 및 위치 탐색
* [**기술 심층 분석**](active-directory-passwords-how-it-works.md) - 작동 방식을 이해하기 위해 심층 분석
* [**질문과 대답**](active-directory-passwords-faq.md) - 어떤 방식으로? 그 이유는 무엇을? 어디서? 누가? 언제? - 많은 분들이 항상 묻는 질문에 대한 답변입니다.
* [**문제 해결**](active-directory-passwords-troubleshoot.md) - SSPR의 일반적인 문제 해결 방법 알아보기

