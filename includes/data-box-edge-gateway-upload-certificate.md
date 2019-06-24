---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: d7a9923d5bd9e357bcd75fae6e0a7d1bcd437a53
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182660"
---
적절 한 SSL 인증서를 올바른 서버에 암호화 된 정보를 전송 했는지 확인 합니다. 암호화, 외에도 인증서 인증을 위해 또한 수 있습니다. 장치의 PowerShell 인터페이스를 통해 신뢰할 수 있는 사용자 고유의 SSL 인증서를 업로드할 수 있습니다.

1. [PowerShell 인터페이스를 연결할](#connect-to-the-powershell-interface)합니다.
2. 사용 된 `Set-HcsCertificate` 인증서를 업로드 하는 cmdlet입니다. 메시지가 표시 되 면 다음 매개 변수를 제공 합니다.

   - `CertificateFilePath` 인증서 파일을 포함 하는 공유에 경로 *.pfx* 형식입니다.
   - `CertificatePassword` -인증서를 보호 하는 데 암호입니다.
   - `Credentials` -Username 및 password 인증서가 포함 된 공유에 액세스할 수 있습니다.

     다음 예제에서는이 cmdlet의 사용법을 보여 줍니다.

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username/Password"
     ```

