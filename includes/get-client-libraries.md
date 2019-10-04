---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 3cd5f59a3dcf3afcd26d16874e7dc77ca0a7e844
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841681"
---
### <a name="install-via-composer"></a>작성기를 통해 설치
1. 프로젝트 루트에 **composer.json** 이라는 파일을 만들고 다음 코드를 추가합니다.
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. 프로젝트 루트에서 **[작성기. phar][composer-phar]** 를 다운로드 합니다.
3. 명령 프롬프트를 열고 프로젝트 루트에서 다음 명령을 실행합니다.
   
    ```
    php composer.phar install
    ```

또는 GitHub의 [AZURE STORAGE PHP 클라이언트 라이브러리로][php-sdk-github] 이동 하 여 소스 코드를 복제 합니다.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar
