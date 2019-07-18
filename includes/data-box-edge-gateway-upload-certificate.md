---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448616"
---
적절 한 SSL 인증서를 올바른 서버에 암호화 된 정보를 전송 했는지 확인 합니다. 암호화, 외에도 인증서 인증을 위해 또한 수 있습니다. 장치의 PowerShell 인터페이스를 통해 신뢰할 수 있는 사용자 고유의 SSL 인증서를 업로드할 수 있습니다.

1. [PowerShell 인터페이스를 연결할](#connect-to-the-powershell-interface)합니다.
2. 사용 된 `Set-HcsCertificate` 인증서를 업로드 하는 cmdlet입니다. 메시지가 표시 되 면 다음 매개 변수를 제공 합니다.

   - `CertificateFilePath` 인증서 파일을 포함 하는 공유에 경로 *.pfx* 형식입니다.
   - `CertificatePassword` -인증서를 보호 하는 데 암호입니다.
   - `Credentials` -인증서가 포함 된 공유에 액세스 하려면 사용자 이름입니다. 메시지가 표시 되 면 네트워크 공유에 암호를 제공 합니다.

     다음 예제에서는이 cmdlet의 사용법을 보여 줍니다.

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

