---
title: Azure AD B2C에서 전화 기반 MFA 보안
titleSuffix: Azure AD B2C
description: Azure Monitor Log Analytics 보고서 및 경고를 사용 하 여 Azure AD B2C 테 넌 트에서 휴대폰 기반 MFA (multi-factor authentication)를 보호 하기 위한 팁에 대해 알아봅니다. Microsoft 통합 문서를 사용 하 여 사기성 전화 인증을 식별 하 고 사기성 등록을 완화 하세요. =
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cc9e0be90c138ba33e1b4dfe11ea6f9c8b7da297
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033557"
---
# <a name="securing-phone-based-multi-factor-authentication-mfa"></a>전화 기반 MFA (multi-factor authentication) 보안

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA)를 사용 하 여 사용자는 확인을 위해 등록 하는 전화 번호에서 자동 음성 통화를 받도록 선택할 수 있습니다. 악의적인 사용자는 MFA 등록 프로세스를 완료 하지 않고 여러 계정을 만들고 전화 통화를 하 여이 방법을 활용할 수 있습니다. 이러한 많은 실패 한 등록 시도는 허용 된 등록 시도를 고갈 하 여 다른 사용자가 Azure AD B2C 테 넌 트의 새 계정에 등록 하지 못하게 할 수 있습니다. 이러한 공격 으로부터 보호 하기 위해 Azure Monitor를 사용 하 여 전화 인증 오류를 모니터링 하 고 사기성 등록을 완화할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작 하기 전에 [Log Analytics 작업 영역](azure-monitor.md)을 만듭니다.

## <a name="create-a-phone-based-mfa-events-workbook"></a>휴대폰 기반 MFA 이벤트 통합 문서 만들기

GitHub의 [Azure AD B2C 보고서 & 경고](https://github.com/azure-ad-b2c/siem#phone-authentication-failures) 리포지토리에는 Azure AD B2C 로그를 기반으로 보고서, 경고 및 대시보드를 만들고 게시 하는 데 사용할 수 있는 아티팩트가 포함 되어 있습니다. 아래 그림에는 전화 관련 오류가 강조 표시 되어 있습니다.

### <a name="overview-tab"></a>개요 탭

**개요** 탭에는 다음과 같은 정보가 표시 됩니다.

- 실패 원인 (지정 된 각 원인에 대해 실패 한 총 전화 인증 수)
- 잘못 된 평판으로 인해 차단 됨
- 실패 한 전화 인증을 포함 하는 IP 주소 (지정 된 각 IP 주소에 대 한 총 실패 한 전화 인증 수)
- IP 주소를 사용 하는 전화 번호-실패 한 전화 인증
- 브라우저 (클라이언트 브라우저 당 전화 인증 오류)
- 운영 체제 (클라이언트 운영 체제 당 전화 인증 오류)

![개요 탭](media/phone-based-mfa/overview-tab.png)

### <a name="details-tab"></a>자세히 탭

**세부** 정보 탭에서 보고 되는 정보는 다음과 같습니다.

- Azure AD B2C 정책-실패 한 전화 인증
- 전화 번호 별 전화 인증 실패-시간 차트 (조정 가능한 시간 표시 막대)
- Azure AD B2C 정책에의 한 전화 인증 실패-시간 차트 (조정 가능한 시간 표시 막대)
- IP 주소로 전화 인증 실패-시간 차트 (조정 가능한 시간 표시 막대)
- 전화 번호를 선택 하 여 오류 세부 정보를 확인 합니다 (자세한 오류 목록에 대해서는 전화 번호를 선택).

![세부 정보 탭 1/3](media/phone-based-mfa/details-tab-1.png)

![세부 정보 탭 2/3](media/phone-based-mfa/details-tab-2.png)

![세부 정보 탭 3/3](media/phone-based-mfa/details-tab-3.png)

## <a name="use-the-workbook-to-identify-fraudulent-sign-ups"></a>통합 문서를 사용 하 여 사기성 등록 식별

통합 문서를 사용 하 여 전화 기반 MFA 이벤트를 이해 하 고 잠재적으로 악의적인 전화 통신 서비스 사용을 식별할 수 있습니다.

1. 다음 질문에 대답 하 여 테 넌 트의 일반적인 기능을 이해 합니다.

   - 휴대폰 기반 MFA를 필요로 하는 지역은 어디에 있나요?
   - 실패 한 전화 기반 MFA 시도에 대해 표시 된 이유를 확인 합니다. 정상 또는 예상으로 간주 되나요?

2. 사기성 등록의 특징을 인식 합니다.

   - **위치 기반**: 사용자가 등록 하지 않을 것으로 간주 되는 위치와 연결 된 모든 계정에 대 한 **IP 주소로 전화 인증 실패** 를 검사 합니다.

   > [!NOTE]
   > 제공 된 IP 주소는 대략적인 지역입니다.

   - **속도 기반**: **실패 한 전화** 인증 실패를 확인 합니다. 실패 한 전화 인증 시도는 비정상적인 횟수 (왼쪽)에서 가장 낮은 (오른쪽) 순으로 정렬 되어 표시 됩니다.

3. 다음 섹션의 단계를 수행 하 여 사기성 등록을 완화 합니다.
 

## <a name="mitigate-fraudulent-sign-ups"></a>사기성 등록 완화

사기성 등록을 완화 하기 위해 다음 작업을 수행 합니다.

- **권장 되** 는 버전의 사용자 흐름을 사용 하 여 다음을 수행 합니다.
     
   - MFA에 대해 [전자 메일 OTP (일회용 암호 기능)를 사용 하도록 설정](phone-authentication-user-flows.md) 합니다 (등록 및 로그인 흐름 모두에 적용 됨).
   - 위치에 따라 로그인을 차단 하 [는 조건부 액세스 정책을 구성](conditional-access-user-flow.md) 합니다 (등록 흐름이 아닌 로그인 흐름에만 적용 됨).
   - API 커넥터를 사용 하 여 reCAPTCHA (등록 흐름에 적용) [와 같은 봇 솔루션과 통합](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) 합니다.

- 사용자가 전화 번호를 확인 하는 드롭다운 메뉴에서 조직과 관련이 없는 국가 코드를 제거 합니다 .이 변경 내용은 향후 등록에 적용 됩니다.
    
   1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

   2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.

   3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.

   4. 사용자 흐름을 선택 하 고 **언어** 를 선택 합니다. 조직의 지리적 위치에 대 한 언어를 선택 하 여 언어 세부 정보 패널을 엽니다. (이 예제에서는 미국에 대해 **영어 en** 을 선택 합니다.) **다단계 인증 페이지** 를 선택 하 고 **기본값 다운로드 (en)** 를 선택 합니다.
 
      ![새 재정의를 업로드 하 여 기본값 다운로드](media/phone-based-mfa/download-defaults.png)

   5. 이전 단계에서 다운로드 한 JSON 파일을 엽니다. 파일에서를 검색 하 `DEFAULT` 고 줄을로 바꿉니다 `"Value": "{\"DEFAULT\":\"Country/Region\",\"US\":\"United States\"}"` . 을로 설정 해야 `Overrides` `true` 합니다.

   > [!NOTE]
   > 요소에서 허용 되는 국가 코드 목록을 사용자 지정할 수 있습니다 `countryList` ( [전화 요소 인증 페이지 예제](localization-string-ids.md#phone-factor-authentication-page-example)참조).

   7. JSON 파일을 저장 합니다. 언어 세부 정보 패널의 **새 재정의 업로드** 에서 수정 된 JSON 파일을 선택 하 여 업로드 합니다.

   8. 패널을 닫고 **사용자 흐름 실행** 을 선택 합니다. 이 예에서는 **주가** 드롭다운에서 사용할 수 있는 유일한 국가 코드 인지 확인 합니다.
 
      ![국가 코드 드롭다운](media/phone-based-mfa/country-code-drop-down.png)

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C에 대 한 Id 보호 및 조건부 액세스](conditional-access-identity-protection-overview.md) 에 대 한 자세한 정보 

- [Azure Active Directory B2C에서 사용자 흐름에 조건부 액세스를 적용 합니다](conditional-access-user-flow.md) .