---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 41d9962657aa81dbe34a52302d1b68ec655f2893
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095292"
---
인증서가 아직 없는 경우 자체 서명 된 인증서를 사용할 수 있습니다. 자체 서명 된 인증서는 CA (인증 기관)에서 서명 하지 않은 보안 인증서 이며, CA에서 서명한 인증서의 보안 보장을 제공 하지 않습니다. 

# <a name="windows"></a>[Windows](#tab/windows)

Windows에서 PowerShell의 [new-selfsignedcertificate](/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet을 사용 하 여 인증서를 생성 합니다.

1. 이 PowerShell 명령을 실행하여 자체 서명된 인증서를 생성합니다. `-Subject` 인수를 애플리케이션 및 Azure AD B2C 테넌트 이름에 적절하게 수정합니다. `-NotAfter` 날짜를 조정하여 인증서에 다른 만료 날짜를 지정할 수도 있습니다.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. **사용자 인증서 관리** > **현재 사용자** > **개인** > **인증서** > *yourappname.yourtenant.onmicrosoft.com* 을 엽니다.
1. 인증서를 선택한 다음 **작업**  >  **모든 태스크**  >  **내보내기** 를 선택 합니다.
1. **예** > **다음** > **예, 프라이빗 키를 내보냅니다.**  > **다음** 을 선택합니다.
1. **내보내기 파일 형식** 의 기본값을 적용합니다.
1. 인증서의 암호를 제공합니다.

.Pfx 파일 암호를 수락 Azure AD B2C 암호는 AES256를 사용 하는 것과 달리 Windows 인증서 저장소 내보내기 유틸리티의 TripleDES-SHA1 옵션을 사용 하 여 암호화 해야 합니다.

# <a name="macos"></a>[macOS](#tab/macos)

MacOS에서 키 집합 액세스의 [인증서 도우미](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) 를 사용 하 여 인증서를 생성 합니다.

1. [Mac의 키 집합 액세스에서 자체 서명 된 인증서를 만드는](https://support.apple.com/guide/keychain-access/kyca8916/mac)방법에 대 한 지침을 따르세요.
1. Mac의 키 집합 액세스 앱에서, 만든 인증서를 선택 합니다.
1. **파일**  >  **내보내기 항목** 을 선택 합니다.
1. 인증서를 저장할 파일 이름을 선택 합니다. 예를 들어 **자체 서명 된 인증서.** p 12입니다.
1. **파일 형식** 으로 **개인 정보 교환 (**.p12)을 선택 합니다.
1. **저장** 을 선택합니다.
1. **암호** 를 입력 한 다음 암호를 **확인** 합니다.
1. 파일 확장명을로 바꿉니다 `.pfx` . 예를 들면 **self-signed-certificate** 입니다.

---