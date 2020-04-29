---
title: Robo 3T를 사용하여 Azure Cosmos DB에 연결
description: Robo 3T 및 Azure Cosmos DB의 MongoDB용 API를 사용하여 Azure Cosmos DB에 연결하는 방법을 알아봅니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: 627849acb902a721c80b98adbd16103b4729591b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114183"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 MongoDB용 API에서 Robo 3T 사용

Robo 3T를 사용하여 Cosmos 계정에 연결하려면 다음을 수행해야 합니다.

* [Robo 3T](https://robomongo.org/) 다운로드 및 설치
* Cosmos DB [연결 문자열](connect-mongodb-account.md) 정보 확보

> [!NOTE]
> 현재 Robo 3T v 1.2이 하 버전은 Cosmos DB의 MongoDB API에서 지원 됩니다.

## <a name="connect-using-robo-3t"></a>Robo 3T를 사용하여 연결

Cosmos 계정을 Robo 3T 연결 관리자에 추가하려면 다음 단계를 수행합니다.

1. [여기](connect-mongodb-account.md)에 설명된 지침에 따라 Azure Cosmos DB의 MongoDB용 API로 구성된 Cosmos 계정에 대한 연결 정보를 검색합니다.

    ![연결 문자열 블레이드의 스크린샷](./media/mongodb-robomongo/connectionstringblade.png)
2. *Robomongo* 응용 프로그램을 실행 합니다.

3. 연결을 관리하려면 **파일**에서 연결 단추를 클릭합니다. 그런 다음 **MongoDB 연결** 창에서 **만들기**를 클릭하면 **연결 설정** 창이 열립니다.

4. **연결 설정** 창에서 이름을 선택합니다. 그런 다음 1단계의 연결 정보에서 **호스트** 및 **포트**를 찾아 **주소** 및 **포트**에 각각 입력합니다.

    ![Robomongo 관리 연결의 스크린샷](./media/mongodb-robomongo/manageconnections.png)
5. **인증** 탭에서 **인증 수행**을 클릭합니다. 그런 다음 데이터베이스(기본값은 *관리자*), **사용자 이름** 및 **암호**를 입력합니다.
**사용자 이름** 및 **암호**는 모두 1단계의 연결 정보에서 찾을 수 있습니다.

    ![Robomongo 인증 탭의 스크린샷](./media/mongodb-robomongo/authentication.png)
6. **SSL** 탭에서 **SSL 프로토콜 사용**을 선택한 다음 **인증 방법**을 **자체 서명된 인증서**로 변경합니다.

    ![Robomongo SSL 탭의 스크린샷](./media/mongodb-robomongo/SSL.png)
7. 마지막으로 **테스트**를 클릭하여 연결할 수 있는지 확인한 다음 **저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- Azure Cosmos DB의 API for MongoDB와 함께 [Studio 3T를 사용](mongodb-mongochef.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB를 사용하여 MongoDB [샘플](mongodb-samples.md)을 살펴봅니다.
