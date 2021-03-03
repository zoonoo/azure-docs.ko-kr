---
title: B2B 게스트 사용자의 일회용 암호 인증 - Azure AD
description: 메일 일회성 암호를 사용하여 Microsoft 계정을 사용하지 않고 B2B 게스트 사용자를 인증하는 방법입니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7961997c6a6736c154b6217ee3f21682d0c4c3fc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688470"
---
# <a name="email-one-time-passcode-authentication"></a>일회용 암호 인증 전자 메일

이 문서에서는 B2B 게스트 사용자에 게 전자 메일 일회용 암호 인증을 사용 하도록 설정 하는 방법을 설명 합니다. 전자 메일 일회용 암호 기능은 Azure AD, Microsoft 계정 (MSA) 또는 Google 페더레이션과 같은 다른 방법으로 인증 될 수 없는 B2B 게스트 사용자를 인증 합니다. 일회성 암호 인증을 사용하면 Microsoft 계정을 만들 필요가 없습니다. 게스트 사용자는 초대를 사용하거나 공유 리소스에 액세스할 때 메일 주소로 전송되는 임시 코드를 요청할 수 있습니다. 그런 다음, 이 코드를 입력하여 로그인을 계속합니다.

![일회용 암호 개요 다이어그램 전자 메일](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> - **2021 년 10 월부터** 모든 기존 테 넌 트에 대해 전자 메일 일회용 암호 기능이 설정 되 고 새 테 넌 트에 대해 기본적으로 사용 하도록 설정 됩니다. 이 기능을 자동으로 설정 하지 않으려면이 기능을 사용 하지 않도록 설정할 수 있습니다. 아래의 [전자 메일 일회용 암호 사용 안 함](#disable-email-one-time-passcode) 을 참조 하세요.
> - 전자 메일 일회성 암호 설정이 **외부 공동 작업 설정** 에서 **모든 id 공급자로** Azure Portal 이동 했습니다.

> [!NOTE]
> 일회성 암호 사용자는 테넌트 컨텍스트를 포함하는 링크를 사용하여 로그인해야 합니다(예: `https://myapps.microsoft.com/?tenantid=<tenant id>` 또는 `https://portal.azure.com/<tenant id>`, 혹은 확인된 도메인의 경우 `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). 애플리케이션 및 리소스에 대한 직접 링크는 테넌트 컨텍스트를 포함하는 한 작동합니다. 게스트 사용자는 현재 테넌트 컨텍스트가 없는 엔드포인트를 사용하여 로그인할 수 없습니다. 예를 들어를 사용 하면 `https://myapps.microsoft.com` `https://portal.azure.com` 오류가 발생 합니다.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>일회성 암호 게스트 사용자를 위한 사용자 환경

전자 메일 일회용 암호 기능을 사용 하도록 설정 하면 [특정 조건을 충족](#when-does-a-guest-user-get-a-one-time-passcode) 하는 새로 초대 된 사용자는 일회성 암호 인증을 사용 합니다. 전자 메일 일회용 암호를 사용 하도록 설정 하기 전에 초대를 받은 게스트 사용자는 계속 해 서 동일한 인증 방법을 사용 합니다.

일회성 암호 인증을 사용하면 게스트 사용자가 직접 링크를 클릭하거나 초대 메일을 사용하여 초대를 사용할 수 있습니다. 두 경우 모두 코드가 게스트 사용자의 메일 주소로 전송된다는 메시지가 브라우저에 표시됩니다. 게스트 사용자가 **코드 보내기** 를 선택합니다.

   ![코드 보내기 단추를 보여주는 스크린샷](media/one-time-passcode/otp-send-code.png)

암호가 사용자의 메일 주소로 전송됩니다. 사용자가 메일에서 암호를 검색하고 브라우저 창에 입력합니다.

   ![코드 입력 페이지를 보여주는 스크린샷](media/one-time-passcode/otp-enter-code.png)

이제 게스트 사용자가 인증되었고 공유 리소스를 보거나 로그인을 계속할 수 있습니다.

> [!NOTE]
> 일회성 암호는 30분 동안 유효합니다. 30분이 지나면 특정 일회성 암호가 더 이상 유효하지 않고 사용자는 새 암호를 요청해야 합니다. 사용자 세션은 24시간 후에 만료됩니다. 해당 시간이 지나면 게스트 사용자는 리소스에 액세스할 때 새 암호를 받습니다. 특히 게스트 사용자가 회사를 떠나거나 더 이상 액세스할 필요가 없는 경우 세션 만료로 보안을 강화합니다.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>게스트 사용자가 일회성 암호를 얻을 때는 언제인가요?

게스트 사용자가 초대를 사용하거나 공유된 리소스의 링크를 사용할 때 일회성 암호를 받는 경우는 다음과 같습니다.

- Azure AD 계정이 없는 경우
- Microsoft 계정이 없는 경우
- 초대하는 테넌트가 @gmail.com 및 @googlemail.com 사용자에 대해 Google 페더레이션을 설정하지 않은 경우

초대할 때 초대를 받는 사용자가 일회성 암호 인증을 사용할 것이라는 표시는 없습니다. 그러나 게스트 사용자가 로그인할 때 다른 인증 방법을 사용할 수 없다면 일회성 암호 인증이 대체 방법이 됩니다.

사용자의 세부 정보에서 **원본** 속성을 확인 하 여 게스트 사용자가 일회성 암호를 사용 하 여 인증 하는지 여부를 확인할 수 있습니다. Azure Portal에서 **Azure Active Directory**  >  **사용자** 로 이동한 다음 사용자를 선택 하 여 세부 정보 페이지를 엽니다.

![OTP의 원본 값을 사용하는 일회용 암호 사용자를 보여주는 스크린샷](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> 사용자가 일회성 암호를 사용하고 나중에 MSA, Azure AD 계정 또는 기타 페더레이션 계정을 얻을 경우 일회성 암호를 사용하여 계속 인증됩니다. 인증 방법을 업데이트하려면 해당 게스트 사용자 계정을 삭제하고 사용자를 다시 초대하면 됩니다.

### <a name="example"></a>예제

게스트 사용자 teri@gmail.com이 Google 페더레이션이 설정되지 않은 Fabrikam에 초대되었습니다. Teri에 Microsoft 계정 없습니다. 인증을 위해 일회용 암호를 받게 됩니다.

## <a name="disable-email-one-time-passcode"></a>전자 메일 일회용 암호 사용 안 함

2021 년 10 월부터 모든 기존 테 넌 트에 대해 전자 메일 일회용 암호 기능이 설정 되 고 새 테 넌 트에 대해 기본적으로 사용 하도록 설정 됩니다. 이 시점에서 Microsoft는 B2B 공동 작업 시나리오에 대 한 관리 되지 않는 ("바 이럴" 또는 "just-in-time") Azure AD 계정 및 테 넌 트를 만들어 초대 상환을 더 이상 지원 하지 않습니다. 게스트 사용자에 게 원활한 대체 인증 방법을 제공 하기 때문에 전자 메일 일회용 암호 기능을 사용 하도록 설정 하 고 있습니다. 그러나이 기능을 사용 하지 않도록 선택 하는 경우이 기능을 사용 하지 않도록 설정 하는 옵션이 있습니다.

> [!NOTE]
>
> 테 넌 트에서 메일 일회용 암호 기능을 사용 하도록 설정 하 고이 기능을 끄면 일회용 암호를 사용한 게스트 사용자는 로그인 할 수 없습니다. 게스트 사용자를 삭제 하 고 다른 인증 방법을 사용 하 여 다시 로그인 할 수 있도록 reinvite 수 있습니다.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>일회용 암호 기능 전자 메일 기능을 사용 하지 않도록 설정 하려면

1. Azure AD 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 탐색 창에서 **Azure Active Directory** 를 선택합니다.

3. **외부 ID** > **모든 ID 공급자** 를 차례로 선택합니다.

4. **전자 메일 일회용 암호** 를 선택한 다음 **게스트에 대해 전자 메일 일회용 암호 사용 안 함** 을 선택 합니다.

   > [!NOTE]
   > 전자 메일 일회성 암호 설정이 **외부 공동 작업 설정** 에서 **모든 id 공급자로** Azure Portal 이동 했습니다.
   > 전자 메일 일회용 암호 옵션 대신 설정/해제가 표시 되 면 이전에 기능을 사용 하도록 설정 했거나 사용 하지 않도록 설정 했거나 미리 보기에 옵트인 했음을 의미 합니다. 기능을 사용 하지 않도록 설정 하려면 **아니요** 를 선택 합니다.
   >
   >![전자 메일 일회용 암호 전환 사용 안 함](media/one-time-passcode/enable-email-otp-disabled.png)

5. **저장** 을 선택합니다.

## <a name="note-for-public-preview-customers"></a>공개 미리 보기 고객에 대 한 참고 사항

이전에 메일 일회용 암호 공개 미리 보기에 옵트인 한 경우 자동 기능을 사용할 수 있도록 10 월 2021 날짜가 자동으로 적용 되지 않으므로 관련 비즈니스 프로세스는 영향을 받지 않습니다. 또한 Azure Portal에서 **게스트 속성의 일회용 암호를 전자 메일로 보내는** 경우 **2021 년 10 월부터 게스트에 대해 전자 메일 일회용 암호를 자동으로 사용 하도록 설정** 하는 옵션이 표시 되지 않습니다. 대신 다음 **예** 또는 **아니요** 를 표시 합니다.

![일회용 암호를 옵트인 (opt in) 하는 전자 메일](media/one-time-passcode/enable-email-otp-opted-in.png)

그러나이 기능을 옵트아웃 (opt out) 하 고 10 월 2021에 자동으로 사용 하도록 설정 하려는 경우 Microsoft Graph API [전자 메일 인증 방법 구성 리소스 종류](/graph/api/resources/emailauthenticationmethodconfiguration)를 사용 하 여 기본 설정으로 되돌릴 수 있습니다. 기본 설정으로 되돌린 후에는 **게스트에 대 한 전자 메일 일회용 암호** 에서 다음 옵션을 사용할 수 있습니다.

![전자 메일 일회용 암호 옵트인 사용](media/one-time-passcode/email-otp-options.png)

- **2021 년 10 월부터 게스트에 대 한 전자 메일 일회용 암호를 자동으로 사용 하도록 설정** 합니다. 기본 테 넌 트에 대해 전자 메일 일회용 암호 기능을 아직 사용 하도록 설정 하지 않은 경우 2021 년 10 월부터 자동으로 설정 됩니다. 이때 기능을 사용 하도록 설정 하려면 추가 작업이 필요 하지 않습니다. 이 기능을 이미 사용 하거나 사용 하지 않도록 설정한 경우에는이 옵션을 사용할 수 없습니다.

- **지금은 게스트에 대 한 전자 메일 일회용 암호를 사용 하도록 설정** 합니다. 테 넌 트에 대해 전자 메일 일회용 암호 기능을 설정 합니다.

- **게스트에 대해 전자 메일 일회용 암호를 사용 하지 않도록 설정** 합니다. 테 넌 트에 대해 전자 메일 일회용 암호 기능을 해제 하 고 10 월 2021에 기능을 설정 하지 않도록 합니다.

## <a name="note-for-azure-us-government-customers"></a>Azure 미국 정부 고객에 대 한 참고 사항

전자 메일 일회용 암호 기능은 Azure 미국 정부 클라우드에서 기본적으로 사용 하지 않도록 설정 되어 있습니다.  

 ![일회용 암호를 사용 하지 않도록 설정 된 전자 메일](media/one-time-passcode/enable-email-otp-disabled.png)

Azure 미국 정부 클라우드에서 전자 메일 일회용 암호 기능을 사용 하도록 설정 하려면 다음을 수행 합니다.

1. Azure AD 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 탐색 창에서 **Azure Active Directory** 를 선택합니다.
3. **조직 관계**   >  **설정** 을 선택 합니다.

   > [!NOTE]
   > - **조직 관계가** 표시 되지 않는 경우 위쪽의 검색 창에서 "외부 id"를 검색 합니다.

4. **전자 메일 일회용 암호** 를 선택 하 고 **예** 를 선택 합니다.
5. **저장** 을 선택합니다.

현재 제한 사항에 대 한 자세한 내용은 [AZURE US 정부 클라우드](current-limitations.md#azure-us-government-clouds)를 참조 하세요.