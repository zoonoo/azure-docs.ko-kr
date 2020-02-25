---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 5e3c4622131528fc2c40a1510aeea3092018d182
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474260"
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

또는 GitHub에서 [Azure Storage PHP 클라이언트 라이브러리][php-sdk-github]로 이동하여 소스 코드를 복제합니다.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar
