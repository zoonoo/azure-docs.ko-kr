---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 9e9057073c8a661e2f3382333abc7ac2778c4ee3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "84680281"
---
### <a name="install-via-composer"></a>작성기를 통해 설치
1. 프로젝트 루트에 **composer.json** 이라는 파일을 만들고 다음 코드를 추가합니다.
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. 프로젝트 루트에 **[composer.phar][composer-phar]** 을 다운로드합니다.
3. 명령 프롬프트를 열고 프로젝트 루트에서 다음 명령을 실행합니다.
   
    ```
    php composer.phar install
    ```

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar
