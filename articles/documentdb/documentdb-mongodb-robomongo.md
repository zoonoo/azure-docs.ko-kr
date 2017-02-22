---
title: "Azure DocumentDB로 MongoDB에 Robomongo 사용 | Microsoft Docs"
description: "MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정으로 Robomongo를 사용하는 방법을 알아봅니다. 현재 미리 보기를 지원합니다."
keywords: robomongo
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: a6aadaae2a9400dc62ab277d89d9a9657833b1b7
ms.openlocfilehash: 141154f9e8236e595f77bd4880c4f63d480da445


---
# <a name="use-robomongo-with-a-documentdb-account-with-protocol-support-for-mongodb"></a>MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정으로 Robomongo 사용
Robomongo를 사용하여 MongoDB에 대한 프로토콜 지원을 사용하는 Azure DocumentDB 계정에 연결하려면 다음을 수행해야 합니다.

* [Robomongo](https://robomongo.org/) 다운로드 및 설치
* MongoDB [연결 문자열](documentdb-connect-mongodb-account.md) 정보에 대한 프로토콜 지원을 사용하는 DocumentDB 계정이 필요합니다.

## <a name="connect-using-robomongo"></a>Robomongo를 사용하여 연결
Robomongo MongoDB 연결에 MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정을 추가하려면 다음 단계를 수행합니다.

1. [여기](documentdb-connect-mongodb-account.md)에 있는 지침을 사용하여 MongoDB 연결 정보에 대한 프로토콜 지원을 사용하는 DocumentDB를 검색합니다.

    ![연결 문자열 블레이드의 스크린샷](./media/documentdb-mongodb-robomongo/connectionstringblade.png)
2. *Robomongo.exe* 실행

3. 연결을 관리하려면 **파일**에서 연결 단추를 클릭합니다. 그런 다음 **MongoDB 연결** 창에서 **만들기**를 클릭하면 **연결 설정** 창이 열립니다.

4. **연결 설정** 창에서 이름을 선택합니다. 그런 다음 1단계의 연결 정보에서 **호스트** 및 **포트**를 찾아 **주소** 및 **포트**에 각각 입력합니다.

    ![Robomongo 연결 관리의 스크린샷](./media/documentdb-mongodb-robomongo/manageconnections.png)
5. **인증** 탭에서 **인증 수행**을 클릭합니다. 그런 다음 데이터베이스(기본값은 *관리자*), **사용자 이름** 및 **암호**를 입력합니다.
**사용자 이름** 및 **암호**는 모두 1단계의 연결 정보에서 찾을 수 있습니다.

    ![Robomongo 인증 탭의 스크린샷](./media/documentdb-mongodb-robomongo/authentication.png)
6. **SSL** 탭에서 **SSL 프로토콜 사용**을 선택한 다음 **인증 방법**을 **자체 서명된 인증서**로 변경합니다.

    ![Robomongo SSL 탭의 스크린샷](./media/documentdb-mongodb-robomongo/SSL.png)
7. 마지막으로 **테스트**를 클릭하여 연결할 수 있는지 확인한 다음 **저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계
* MongoDB [샘플](documentdb-mongodb-samples.md)에 대한 프로토콜 지원을 사용하여 DocumentDB를 탐색합니다.



<!--HONumber=Jan17_HO4-->


