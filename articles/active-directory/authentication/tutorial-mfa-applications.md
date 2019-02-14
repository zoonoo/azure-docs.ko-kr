---
title: Azure Multi-Factor Authentication 파일럿 사용
description: 이 자습서에서는 파일럿 사용자 그룹에 Azure AD Multi-Factor Authentication을 사용하도록 설정할 것입니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fdf88ed6cedaa38676a56536ff1eda7ee6bca66
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204800"
---
# <a name="tutorial-complete-an-azure-multi-factor-authentication-pilot-roll-out"></a>자습서: Azure Multi-Factor Authentication 파일럿 배포 완료

이 자습서에서는 Azure Portal에 로그인할 때 Azure MFA(Azure Multi-Factor Authentication)를 사용하도록 조건부 액세스 정책을 구성하는 방법을 안내합니다. 이 정책은 특정 파일럿 사용자 그룹에 배포되고 테스트됩니다. 조건부 액세스를 사용하여 Azure MFA를 배포하면 기존에 적용된 방법에 비해 조직과 관리자에게 상당히 많은 유연성이 제공됩니다.

> [!div class="checklist"]
> * Azure Multi-Factor Authentication 사용
> * Azure Multi-Factor Authentication 테스트

## <a name="prerequisites"></a>필수 조건

* 적어도 평가판 라이선스가 설정된 작동 중인 Azure AD 테넌트
* 전역 관리자 권한이 있는 계정
* 테스트용 암호를 알고 있는 관리자가 아닌 테스트 사용자. 사용자를 만들어야 하는 경우 [빠른 시작: Azure Active Directory에 새 사용자 추가](../add-users-azure-active-directory.md)의 정보를 사용할 수 있습니다.
* 비 관리자 사용자가 멤버인지 테스트하는 데 사용할 파일럿 그룹. 그룹을 만들어야 하는 경우 [Azure Active Directory에서 그룹 만들기 및 멤버 추가](../active-directory-groups-create-azure-portal.md) 문서를 참조하세요.

## <a name="enable-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 사용

1. 전역 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** , **조건부 액세스**로 이동합니다.
1. **새 정책**을 선택합니다.
1. 정책 이름을 **MFA 파일럿**으로 지정합니다.
1. **사용자 및 그룹** 아래에서 **사용자 및 그룹 선택** 라디오 단추를 선택합니다.
    * 이 문서의 필수 구성 요소 섹션에서 만든 파일럿 그룹을 선택합니다.
    * **완료**를 클릭합니다.
1. **클라우드 앱** 아래에서 **앱 선택** 라디오 단추를 선택합니다.
    * Azure Portal에 대한 클라우드 앱은 **Microsoft Azure Management**입니다.
    * **선택** 클릭
    * **완료**를 클릭합니다.
1. **조건** 섹션은 건너뜁니다.
1. **권한 부여** 아래에서 **액세스 권한 부여** 라디오 단추를 선택합니다.
    * **Multi-Factor Authentication 필요** 확인란을 선택합니다.
    * **선택** 클릭
1. **세션** 섹션을 건너뜁니다.
1. **정책 사용** 토글을 **켜기**로 설정합니다.
1. **만들기**

## <a name="test-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 테스트

조건부 액세스 정책이 작동하는지 증명하려면 MFA를 요구하지 않아야 하는 리소스에 로그인 후 MFA가 필요한 Azure Portal에 로그인하는 테스트를 수행하세요.

1. InPrivate 또는 incognito 모드에서 새 브라우저 창을 열고 [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)으로 이동합니다.
   * 이 문서의 필수 조건 섹션에서 만든 테스트 사용자로 로그인하여 MFA를 완료하라고 요청하지는 않는지 확인합니다.
   * 브라우저 창을 닫습니다.
2. InPrivate 또는 incognito 모드에서 새 브라우저 창을 열고 [https://portal.azure.com](https://portal.azure.com)으로 이동합니다.
   * 이 문서의 필수 조건 섹션에서 만든 테스트 사용자로 로그인하면 이제 Azure Multi-Factor Authentication을 등록하여 사용해야 합니다.
   * 브라우저 창을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 구성한 기능을 더 이상 사용하지 않으려면 다음과 같이 변경합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** , **조건부 액세스**로 이동합니다.
1. 앞에서 만든 조건부 액세스 정책을 선택합니다.
1. **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Multi-Factor Authentication을 사용하도록 설정했습니다. Azure Identity Protection을 셀프 서비스 암호 재설정 및 Multi-Factor Authentication 환경에 통합하는 방법을 보려면 그 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [로그인 시 위험 평가](tutorial-risk-based-sspr-mfa.md)
