---
title: Azure AD SSPR 파일럿 사용
description: 이 자습서에서는 사용자 파일럿 그룹에 Azure AD 셀프 서비스 암호 재설정을 사용하도록 설정할 것입니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fe472a45d99f966c851934ea44a346326cb4201
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175152"
---
# <a name="tutorial-complete-an-azure-ad-self-service-password-reset-pilot-roll-out"></a>자습서: Azure AD 셀프 서비스 암호 재설정 파일럿 배포 완료

이 자습서에서는 조직에 Azure AD SSPR(셀프 서비스 암호 재설정)을 파일럿 배포하고 관리자가 아닌 계정을 사용하여 테스트합니다.

셀프 서비스 암호 재설정 테스트에는 관리자가 아닌 계정을 사용해야 합니다. Microsoft는 관리자 계정의 암호 재설정 정책을 관리하며 보다 강력한 인증 방법을 사용하도록 요구하고 있습니다. 이 정책은 보안 질문 및 답변 사용을 허용하지 않으며 재설정에 두 가지 방법을 사용할 것을 요구합니다.

> [!div class="checklist"]
> * 셀프 서비스 암호 재설정 사용
> * 사용자로 SSPR 테스트

## <a name="prerequisites"></a>필수 조건

* 전역 관리자 계정

## <a name="enable-self-service-password-reset"></a>셀프 서비스 암호 재설정 사용

1. 전역 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory**로 이동하여 **암호 재설정**을 선택합니다.
1. 조직의 사용자 하위 집합에 셀프 서비스 암호를 사용하도록 설정하여 파일럿 그룹을 시작합니다.
   * **속성** 페이지의 **셀프 서비스 암호 재설정이 사용하도록 설정됨** 옵션 아래에서 **선택한 사용자**를 선택하고 파일럿 그룹을 고릅니다.
      * 선택한 특정 Azure AD 그룹 구성원만 SSPR 기능을 사용할 수 있습니다. 개념 증명을 위해 이 기능을 배포할 때 사용자 그룹을 정의하고 이 설정을 사용하는 것이 좋습니다. 여기서는 보안 그룹 중첩이 지원됩니다.
      * 선택한 그룹의 사용자에게 적절한 라이선스가 부여되었는지 확인합니다.
   * 페이지 맨 아래에 있는 **저장**
1. **인증 방법** 페이지에서
   * **재설정에 필요한 방법 수**를 **2**로 설정합니다.
   * 조직에서 허용하려는 **사용자가 사용할 수 있는 방법**을 선택합니다. 이 자습서에서는 **이메일**, **휴대폰** 및 **사무실 전화** 확인란을 선택합니다.
   * 페이지 맨 아래에 있는 **저장**
1. **등록** 페이지에서
   * **로그인 시 사용자가 등록하도록 요구**에서 **예**를 선택합니다.
   * **사용자가 인증 정보를 다시 확인해야 하기 전의 일 수**를 **180**으로 설정합니다.
   * 페이지 맨 아래에 있는 **저장**
1. **알림** 페이지에서
   * **사용자에게 암호 재설정에 대해 알림**을 **예**로 설정합니다.
   * **다른 관리자가 암호를 재설정하면 모든 관리자에게 알림**을 **예**로 설정합니다.
1. **사용자 지정** 페이지에서
   * Microsoft는 **기술 지원팀 링크 사용자 지정**을 **예**로 설정하고, **사용자 지정 기술 지원팀 이메일 또는 URL** 필드에서 사용자가 조직으로부터 추가 도움을 얻을 수 있는 이메일 주소 또는 웹 페이지 URL을 제공할 것을 권장하고 있습니다.
   * 이 자습서에서는 **기술 지원팀 링크 사용자 지정**을 **아니요**로 두겠습니다.

파일럿 그룹의 클라우드 사용자에 대해 셀프 서비스 암호 재설정이 구성되었습니다.

## <a name="test-sspr-as-a-user"></a>사용자로 SSPR 테스트

파일럿 그룹의 구성원인 비 관리자 테스트 사용자를 사용하여 셀프 서비스 암호 재설정을 테스트합니다. **관리자 역할이 할당된 계정을 사용할 경우 인증 방법 및 수가 Microsoft 관리 관리자 정책으로 선택한 것과 다를 수 있습니다.**

1. 새 InPrivate 또는 incognito 모드 브라우저 창을 엽니다.
1. [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)에서 등록 포털을 사용하여 셀프 서비스 암호 재설정 사용자 등록을 테스트합니다.
1. 동일한 테스트 사용자를 사용하여 셀프 서비스 암호 재설정 포털 [https://aka.ms/sspr](https://aka.ms/sspr)로 이동한 후 이전 단계에서 입력한 정보를 사용하여 암호를 재설정합니다.
1. 이제 성공적으로 암호를 재설정할 수 있을 것입니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 구성한 기능을 더 이상 사용하지 않으려면 다음과 같이 변경합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory**로 이동하여 **암호 재설정**을 선택합니다.
1. **속성** 페이지의 **셀프 서비스 암호 재설정이 사용하도록 설정됨** 옵션에서 **없음**을 선택합니다.
1. 페이지 맨 아래에 있는 **저장**

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure AD 셀프 서비스 암호 재설정을 사용하도록 설정했습니다. 온-프레미스 Active Directory Domain Services 인프라를 셀프 서비스 암호 재설정 환경에 통합하는 방법을 보려면 그 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [SSPR 온-프레미스 쓰기 저장(writeback) 통합 사용](tutorial-enable-writeback.md)
