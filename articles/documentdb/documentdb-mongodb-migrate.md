---
title: "MongoDB에 대한 프로토콜 지원을 사용하는 Azure DocumentDB 계정으로 데이터 마이그레이션 | Microsoft Docs"
description: "mongoimport 및 mongorestore를 사용하여 MongoDB에 대한 프로토콜 지원을 통해 DocumentDB 계정으로 데이터를 가져오는 방법을 알아봅니다. 현재 미리 보기를 사용할 수 있습니다."
keywords: "마이그레이션"
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
ms.date: 12/07/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 2dc98400ed4eaa2263d73f0718f5eaee260d48f5
ms.openlocfilehash: 80665bc84948696b05b04536a066ebebb5a81e89


---
# <a name="migrate-data-to-documentdb-with-protocol-support-for-mongodb"></a>MongoDB에 대한 프로토콜 지원을 사용하여 DocumentDB로 데이터 마이그레이션
MongoDB에 대한 프로토콜 지원을 사용하는 Azure DocumentDB 계정으로 데이터를 마이그레이션하려면 다음을 수행해야 합니다.

* [MongoDB Download Center](https://www.mongodb.com/download-center)에서 *mongoimport.exe* 또는 *mongorestore.exe*를 다운로드합니다.
* [MongoDB 연결 문자열에 대한 DocumentDB 지원](documentdb-connect-mongodb-account.md)을 받으세요.

## <a name="before-you-begin"></a>시작하기 전에

* 처리량 늘리기: 데이터 마이그레이션 기간은 컬렉션에 대해 설정한 처리량에 따라 다릅니다. 대량 데이터 마이그레이션의 경우 처리량을 늘려야 합니다. 마이그레이션을 완료한 후에는 비용을 절약하기 위해 처리량을 줄이세요. [Azure Portal](https://portal.azure.com)의 처리량 늘리기에 대한 자세한 내용은 [DocumentDB에서 성능 수준 및 가격 책정 계층](documentdb-performance-levels.md)을 참조하세요.

* SSL 사용: DocumentDB에는 엄격한 보안 요구 사항과 표준이 있습니다. 계정과 상호 작용하는 경우 SSL을 사용해야 합니다. 나머지 문서의 절차에서는 *mongoimport* 및 *mongorestore*에 대해 SSL을 사용하도록 설정하는 방법을 설명합니다.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>연결 문자열 정보(호스트, 포트, 사용자 이름 및 암호) 찾기

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **NoSQL(DocumentDB)** 항목을 클릭합니다.
2. **구독** 창에서 계정 이름을 선택합니다.
3. **연결 문자열** 블레이드에서 **연결 문자열**을 클릭합니다.  
오른쪽 창에는 계정에 성공적으로 연결하는 데 필요한 모든 정보가 포함되어 있습니다.

    !["연결 문자열" 블레이드](./media/documentdb-mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-documentdb-with-protocol-support-for-mongodb-with-mongoimport"></a>mongoimport를 사용하여 MongoDB에 대한 프로토콜 지원을 통해 DocumentDB로 데이터 가져오기

데이터를 DocumentDB 계정으로 가져오려면 다음 템플릿을 사용하여 가져오기를 실행합니다. *호스트*, *사용자 이름* 및 *암호*를 계정과 관련된 값으로 채웁니다.  

템플릿:

    mongoimport.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

예제:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-documentdb-with-protocol-support-for-mongodb-with-mongorestore"></a>mongorestore를 사용하여 MongoDB에 대한 프로토콜 지원을 통해 DocumentDB로 데이터 가져오기

DocumentDB 계정으로 데이터를 복원하려면 다음 템플릿을 사용하여 가져오기를 실행합니다. *호스트*, *사용자 이름* 및 *암호*를 계정과 관련된 값으로 채웁니다.

템플릿:

    mongorestore.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

예제:

    mongorestore.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07

## <a name="next-steps"></a>다음 단계
* 자세한 내용을 보려면 [MongoDB 샘플에 대한 DocumentDB 프로토콜 지원](documentdb-mongodb-samples.md)을 탐색합니다.



<!--HONumber=Dec16_HO3-->


