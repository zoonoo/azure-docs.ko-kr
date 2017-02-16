---
title: "MongoDB 앱에서 사용할 DocumentDB 계정 만들기 | Microsoft 문서"
description: "이 자습서를 사용하면 MongoDB에 대한 프로토콜을 지원하는 DocumentDB 계정을 만드는 방법에 대해 알아볼 수 있습니다. 현재 미리 보기만 지원됩니다."
keywords: "mongodb 앱, mongodb 앱"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 413f3ae1-a68e-43be-b0d7-fa2987644f3e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 9b83a0878f45bf3a79d00ad77965308d95f33d28
ms.openlocfilehash: 797ee3b1209e5abf545f0614c408d94391a29c8b


---

# <a name="create-a-documentdb-account-for-use-with-mongodb-apps"></a>MongoDB 앱에서 사용할 DocumentDB 계정 만들기
이제 DocumentDB 데이터베이스는 MongoDB에 대해 작성된 앱의 데이터 저장소로 사용될 수 있습니다. 이 기능을 사용하려면 Azure 계정과 DocumentDB 계정이 필요합니다. 이 자습서에서는 MongoDB 앱에서 사용할 DocumentDB 계정을 만드는 과정을 안내합니다. 

Azure Portal 또는 Azure Resource Manager 템플릿 포함 Azure CLI를 사용하는 MongoDB 계정에 대한 지원으로 DocumentDB 계정을 만들 수 있습니다. 이 문서에서는 Azure Portal을 사용하는 MongoDB 계정에 대한 지원으로 DocumentDB를 만드는 방법을 보여 줍니다. Azure Resource Manager 포함 Azure CLI를 사용하는 계정을 만들려면 [MongoDB 계정 만들기에 대한 지원으로 DocumentDB 자동화](documentdb-automation-resource-manager-cli.md#quick-create-documentdb-with-mongodb-api-account)를 참조하세요.

## <a name="prerequisite"></a>필수 요소
Azure 계정. Azure 계정이 없으면 지금 [무료 Azure 계정](https://azure.microsoft.com/free/)을 만듭니다.
## <a name="create-a-documentdb-account"></a>DocumentDB 계정 만들기

1. 인터넷 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 탐색 영역에서 **NoSQL(DocumentDB)**을 클릭합니다.

    ![DocumentDB NoSQL 항목을 강조 표시하는 포털의 왼쪽 탐색 영역 스크린샷](./media/documentdb-create-mongodb-account/portalleftnav.png)

3. 또는 **추가 서비스 >**를 클릭하고, 위쪽 검색 표시줄에서 **DocumentDB**를 입력하고, **NoSQL(DocumentDB)**을 클릭합니다.

    ![DocumentDB NoSQL 항목을 검색하는 추가 서비스 블레이드의 스크린샷](./media/documentdb-create-mongodb-account/more-services-search.PNG)

4. **NoSQL(DocumentDB)** 블레이드 위쪽의 작업 표시줄 위쪽에 있는 **+ 추가**를 클릭합니다.

    ![DocumentDB NoSQL 리소스 블레이드의 추가 단추 스크린샷](./media/documentdb-create-mongodb-account/add-documentdb-account.png)

5. **DocumentDB 계정** 블레이드에서 계정에 대한 구성을 원하는 대로 지정합니다.

   ![MongoDB 블레이드에 대한 프로토콜 지원을 사용하는 새 DocumentDB의 스크린샷](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.PNG)

    - **ID** 상자에 계정을 식별하기 위한 이름을 입력합니다.  **ID**의 유효성이 검사되면 **ID** 상자에 녹색 확인 표시가 나타납니다. **ID** 값은 URI 내의 호스트 이름이 됩니다. **ID** 는 소문자, 숫자 및 '-' 문자만 포함할 수 있으며, 3자에서 50자 사이여야 합니다. 선택한 끝점 이름에 *documents.azure.com* 이 추가되고, 그 결과가 계정 끝점이 됩니다.

    - **NoSQL API**에서 경우 **MongoDB**를 선택합니다. 이렇게 하면 DocumentDB 데이터베이스와 상호 작용하는 데 사용할 통신 API를 지정합니다.

    - **구독**에서는 계정에 사용할 Azure 구독을 선택합니다. 계정에 구독이 하나뿐인 경우 해당 계정이 기본적으로 선택됩니다.

    - **리소스 그룹**에서 계정에 대한 리소스 그룹을 선택하거나 만듭니다.  기본적으로 Azure 구독에 따라 기존 리소스 그룹이 선택됩니다.  그러나 계정을 추가할 새로운 리소스 그룹을 만들어 선택할 수 있습니다. 자세한 내용은 [Azure 포털을 사용하여 Azure 리소스 관리](../azure-portal/resource-group-portal.md)를 참조하세요.

    - **위치** 를 사용하여 계정을 호스트할 지리적 위치를 지정합니다.

6. 새 계정 옵션을 구성하면 **만들기**를 클릭합니다.  계정을 만드는 데 몇 분 정도 걸릴 수 있습니다.

   [알림] 허브에서 진행률을 모니터링할 수 있습니다.  

   ![DocumentDB 계정을 만들고 있음을 보여 주는 알림 허브의 스크린샷](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-deployment-status.png)  

7. 새 계정에 액세스하려면 왼쪽 메뉴에서 **DocumentDB(NoSQL)**를 클릭합니다. 일반 DocumentDB 및 Mongo 프로토콜 지원 계정 DocumentDB 목록에서 새 계정의 이름을 클릭합니다.
8. 이제 DocumentDB 계정을 MongoDB 앱에서 사용할 준비가 되었습니다.

   ![기본 계정 블레이드의 스크린샷](./media/documentdb-create-mongodb-account/defaultaccountblade.png)

## <a name="next-steps"></a>다음 단계
* MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정을 [연결하는](documentdb-connect-mongodb-account.md) 방법을 알아봅니다.



<!--HONumber=Jan17_HO2-->


