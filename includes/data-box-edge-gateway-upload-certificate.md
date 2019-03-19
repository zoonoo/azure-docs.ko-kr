---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 216380cf7069468d13c4e533fc90b2596aa211c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58114675"
---
적절 한 SSL 인증서를 올바른 서버에 암호화 된 정보를 전송 했는지 확인 합니다. 암호화, 외에도 인증서 인증을 위해 또한 수 있습니다. 장치의 PowerShell 인터페이스를 통해 신뢰할 수 있는 사용자 고유의 SSL 인증서를 업로드할 수 있습니다.

1. [PowerShell 인터페이스를 연결할](#connect-to-the-powershell-interface)합니다.
2. 사용 된 `Set-HcsCertificate` 인증서를 업로드 하는 cmdlet입니다. 메시지가 표시 되 면 다음 매개 변수를 제공 합니다.

   - `CertificateFilePath` 인증서 파일을 포함 하는 공유에 경로 *.pfx* 형식입니다.
   - `CertificatePassword` -인증서를 보호 하는 데 암호입니다.
   - `Credentials` -Username 및 password 인증서가 포함 된 공유에 액세스할 수 있습니다.

     다음 예제에서는이 cmdlet의 사용법을 보여 줍니다.

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credentials "Username/Password"
     ```

