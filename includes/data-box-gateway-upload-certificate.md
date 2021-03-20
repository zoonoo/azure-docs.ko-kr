---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 27a4d775b8d88e02be69655e5adfc6155f867ef6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582174"
---
적절한 SSL 인증서를 사용하면 암호화된 정보를 올바른 서버에 보낼 수 있습니다. 암호화 외에도 인증서를 인증에 사용할 수 있습니다. 장치의 PowerShell 인터페이스를 통해 신뢰할 수 있는 SSL 인증서를 업로드할 수 있습니다.

1. [PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)합니다.
2. Cmdlet을 사용 `Set-HcsCertificate` 하 여 인증서를 업로드 합니다. 메시지가 표시 되 면 다음 매개 변수를 제공 합니다.

   - `CertificateFilePath` - *.Pfx* 형식의 인증서 파일을 포함 하는 공유에 대 한 경로입니다.
   - `CertificatePassword` -인증서를 보호 하는 데 사용 되는 암호입니다.
   - `Credentials` -인증서가 포함 된 공유에 액세스 하기 위한 사용자 이름입니다. 메시지가 표시되면 네트워크 공유에 대한 암호를 제공합니다.

     다음 예에서는이 cmdlet을 사용 하는 방법을 보여 줍니다.

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```
