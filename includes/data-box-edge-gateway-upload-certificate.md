---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "67448616"
---
적절한 SSL 인증서를 사용하면 암호화된 정보를 올바른 서버에 보낼 수 있습니다. 암호화 외에도 인증서를 인증에 사용할 수 있습니다. 디바이스의 PowerShell 인터페이스를 통해 신뢰할 수 있는 고유한 SSL 인증서를 업로드할 수 있습니다.

1. [PowerShell 인터페이스에 연결하세요](#connect-to-the-powershell-interface).
2. `Set-HcsCertificate` cmdlet을 사용하여 인증서를 업로드합니다. 메시지가 표시되면 다음 매개 변수를 제공합니다.

   - `CertificateFilePath` - *.pfx* 형식의 인증서 파일을 포함하는 공유에 대한 경로입니다.
   - `CertificatePassword` - 인증서를 보호하는 데 사용되는 암호입니다.
   - `Credentials` - 인증서가 포함된 공유에 액세스하기 위한 사용자 이름입니다. 메시지가 표시되면 네트워크 공유에 대한 암호를 제공합니다.

     다음 예제에서는 이 cmdlet의 사용법을 보여줍니다.

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

