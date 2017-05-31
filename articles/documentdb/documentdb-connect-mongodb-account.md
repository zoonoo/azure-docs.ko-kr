---
title: "Azure Cosmos DB 계정에 대한 MongoDB 연결 문자열 | Microsoft Docs"
description: "MongoDB 연결 문자열을 사용하여 MongoDB 앱을 Azure Cosmos DB 계정에 연결하는 방법에 대해 알아봅니다."
keywords: "MongoDB 연결 문자열"
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 95fe71c87a3f70174b59cc866d7d399b7e91720c
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---

# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Azure Cosmos DB에 MongoDB 응용 프로그램 연결
MongoDB 연결 문자열을 사용하여 MongoDB 앱을 Azure Cosmos DB 계정에 연결하는 방법에 대해 알아봅니다. MongoDB 앱을 Azure Cosmos DB 데이터베이스에 연결하면 MongoDB 앱의 데이터 저장소로 Azure Cosmos DB 데이터베이스를 사용할 수 있습니다. 

이 자습서에서는 연결 문자열 정보를 검색하는 다음 두 가지 방법을 제공합니다.

- [빠른 시작 방법](#QuickstartConnection) - .NET, Node.js, MongoDB Shell, Java 및 Python 드라이버와 함께 사용
- [사용자 지정 연결 문자열 방법](#GetCustomConnection) - 다른 드라이버와 함께 사용

## <a name="prerequisites"></a>필수 조건

- Azure 계정. Azure 계정이 없으면 지금 [무료 Azure 계정](https://azure.microsoft.com/free/)을 만듭니다. 
- Azure Cosmos DB 계정. 자세한 지침은 [MongoDB 앱에서 사용할 Azure Cosmos DB 계정 만들기](documentdb-create-mongodb-account.md)를 참조하세요.

## <a id="QuickstartConnection"></a>빠른 시작을 사용하여 MongoDB 연결 문자열 가져오기
1. 인터넷 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Cosmos DB** 블레이드에서 MongoDB API 계정을 선택합니다. 
3. 계정 블레이드의 **왼쪽 탐색** 막대에서 **빠른 시작**을 클릭합니다. 
4. 플랫폼(*.NET 드라이버*, *Node.js 드라이버*, *MongoDB 셸*, *Java 드라이버*, *Python 드라이버*)을 선택합니다. 드라이버나 도구가 목록에 없더라도 계속해서 더 많은 연결 코드 조각을 문서화하므로 걱정하지 마세요. 아래에서 보려는 내용에 대해 의견을 보내고 사용자 고유의 연결을 만드는 방법을 알아보려면 [계정 연결 문자열 정보 가져오기](#GetCustomConnection)를 참조하세요.
5. 코드 조각을 복사하여 MongoDB 앱에 붙여넣으면 계속 진행할 준비가 된 것입니다.

    ![빠른 시작 블레이드의 스크린샷](./media/documentdb-connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> 사용자 지정할 MongoDB 연결 문자열 가져오기
1. 인터넷 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Cosmos DB** 블레이드에서 MongoDB API 계정을 선택합니다. 
3. 계정 블레이드의 **왼쪽 탐색** 막대에서 **연결 문자열**을 클릭합니다. 
4. 미리 구성된 연결 문자열을 포함하는 MongoDB에 드라이버를 사용하여 계정에 연결하는 데 필요한 모든 정보를 가진 **연결 문자열 정보** 블레이드가 열립니다.

    ![연결 문자열 블레이드의 스크린샷](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>연결 문자열 요구 사항
> [!Important]
> Azure Cosmos DB에는 엄격한 보안 요구 사항과 표준이 있습니다. Azure Cosmos DB 계정에는 **SSL**을 통한 인증 및 보안 통신이 필요합니다.
>
>

Azure Cosmos DB가 두 가지 특정 요구 사항을 포함한 표준 MongoDB 연결 문자열 URI 형식을 지원해야 합니다. Azure Cosmos DB 계정에는 인증 및 SSL을 통한 보안 통신이 필요합니다.  따라서 연결 문자열 형식은 다음과 같습니다.

    mongodb://username:password@host:port/[database]?ssl=true

여기서 이 문자열의 값은 위에 표시된 연결 문자열 블레이드에서 사용할 수 있습니다.

* Username(필수)
  * Azure Cosmos DB 계정 이름
* 암호(필수)
  * Azure Cosmos DB 계정 암호
* 호스트(필수)
  * Azure Cosmos DB 계정의 FQDN
* 포트(필수)
  * 10250
* 데이터베이스(선택 사항)
  * 연결에서 사용하는 기본 데이터베이스 (데이터베이스가 제공되지 않는 경우 기본 데이터베이스는 "test"입니다)
* ssl=true(필수)

예를 들어 위의 연결 문자열 정보에 표시된 계정을 사용하는 것이 좋습니다.  유효한 연결 문자열은 다음과 같습니다.

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@anhohmongo.documents.azure.com:10250/mydatabase?ssl=true

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB: MongoDB API 계정으로 [MongoChef를 사용](documentdb-mongodb-mongochef.md)하는 방법을 알아봅니다.
* Azure Cosmos DB: MongoDB API [샘플](documentdb-mongodb-samples.md)을 살펴봅니다.

