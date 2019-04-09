---
title: Azure AD SSPR 및 Multi-factor Authentication (미리 보기)-Azure Active Directory에 대 한 결합 된 등록 시작
description: Azure AD 다단계 인증을 결합 하는 사용 하도록 설정 하 고 셀프 서비스 암호 재설정 등록 (미리 보기)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3baf2690ae07b87bb4d5dba30fcd20f62a1a4506
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280573"
---
# <a name="enable-combined-security-information-registration-preview"></a>보안 정보 등록 결합 사용 (미리 보기)

새 환경을 설정 하기 전에 문서를 검토 [보안 정보 등록 (미리 보기)를 결합](concept-registration-mfa-sspr-combined.md) 기능과이 기능의 효과 이해 하도록 합니다.

![향상 된 환경 결합 된 보안 정보 등록](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Azure Multi-factor Authentication 및 Azure Active Directory (Azure AD) 셀프 서비스 암호 재설정에 대 한 통합된 보안 정보 등록에는 Azure AD의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

## <a name="enable-combined-registration"></a>결합 등록 사용

결합 된 등록을 사용 하도록 설정 하려면 다음이 단계를 완료 합니다.

1. 사용자 관리자 또는 전역 관리자로 Azure portal에 로그인 합니다.
2. 로 이동 **Azure Active Directory** > **사용자 설정** > **액세스 패널 미리 보기 기능에 대 한 설정을 관리**합니다.
3. 아래 **사용자가 사용할 수 미리 보기 기능 등록 하 고 보안 정보 관리에 대 한-새로 고침**에 대해 사용 하도록 설정할를 **선택한** 사용자 또는 그룹 **모든** 사용자입니다.

   ![모든 사용자에 대해 결합 된 보안 정보 미리 보기 환경을 사용 하도록 설정](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> 2019 년 3 월부터, 전화 통화 옵션을 Multi-factor Authentication 및 Azure AD 체험/평가판 테 넌 트에 사용자가 SSPR 사용할 수 없습니다. SMS 메시지는이 변경의 영향을 받지 않습니다. 전화 통화 옵션을 사용할 수 있습니다 사용자에 게 유료 Azure AD 테 넌 트입니다.

> [!NOTE]
> 결합 된 등록, 등록 또는 자신의 전화 번호를 확인 하는 사용자를 사용 하거나 새 환경을 통해 모바일 앱 수 사용 하 여 해당 Multi-factor Authentication 및 SSPR에 대 한 이러한 메서드는 Multi-factor Authentication 및 SSPR에 설정 된 경우 정책입니다. 이전 SSPR 등록으로 이동 하는 사용자 페이지에서 다음에이 환경을 비활성화 하면 `https:/aka.ms/ssprsetup` 페이지에 액세스 하기 전에 multi-factor authentication을 수행 해야 합니다.

Internet Explorer에서 사이트 할당 목록 영역을 구성한 경우 다음 사이트를 동일한 영역에 수 해야 합니다.

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>다음 단계

[Multi-factor Authentication 및 SSPR에 대 한 사용 가능한 메서드](concept-authentication-methods.md)

[셀프 서비스 암호 재설정 구성](howto-sspr-deployment.md)

[Azure Multi-Factor Authentication 구성](howto-mfa-getstarted.md)

[결합 된 보안 정보 등록 문제 해결](howto-registration-mfa-sspr-combined-troubleshoot.md)