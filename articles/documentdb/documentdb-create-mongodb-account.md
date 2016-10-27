<properties 
    pageTitle="MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정 만들기 | Microsoft Azure" 
    description="MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정을 만드는 방법을 알아봅니다. 현재 미리 보기를 지원합니다." 
    services="documentdb" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="anhoh"/>


# <a name="how-to-create-a-documentdb-account-with-protocol-support-for-mongodb-using-the-azure-portal"></a>Azure 포털을 사용하여 MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정을 만드는 방법

MongoDB에 대한 프로토콜 지원을 사용하는 Azure DocumentDB 계정을 만들려면 다음을 수행해야 합니다.

- Azure 계정이 있어야 합니다. 없는 경우 [무료 Azure 계정](https://azure.microsoft.com/free/) 을 얻을 수 있습니다.

## <a name="create-the-account"></a>계정을 만듭니다.  

MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정을 만들려면 다음 단계를 수행합니다.

1. 새 창에서 [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기**, **데이터 + 저장소**, **모두 확인**을 차례로 클릭한 다음 "DocumentDB 프로토콜"에 대한 **데이터 + 저장소** 범주를 검색합니다. **DocumentDB - MongoDB에 대한 프로토콜 지원**을 클릭합니다.

    ![마켓플레이스 및 데이터의 스크린샷 + 저장소 검색 블레이드, DocumentDB - MongoDB, Mongo 데이터베이스에 대한 프로토콜 지원 강조 표시](./media/documentdb-create-mongodb-account/marketplacegallery2.png)

3. 또는 **데이터 + 저장소** 범주의 **저장소**에서 **추가**를 클릭하고 **추가 로드**를 한 번 이상 클릭하여 **DocumentDB - MongoDB에 대한 프로토콜 지원**을 표시합니다. **DocumentDB - MongoDB에 대한 프로토콜 지원**을 클릭합니다.

    ![마켓플레이스 및 데이터의 스크린샷 + 저장소 블레이드, DocumentDB - MongoDB, Mongo 데이터베이스에 대한 프로토콜 지원 강조 표시](./media/documentdb-create-mongodb-account/marketplacegallery1.png)

4. **DocumentDB - MongoDB에 대한 프로토콜 지원(미리 보기)** 블레이드에서 **만들기**를 클릭하여 미리 보기 등록 프로세스를 시작합니다.

    ![DocumentDB - Azure 포털에서 MongoDB 블레이드에 대한 프로토콜 지원](./media/documentdb-create-mongodb-account/marketplacegallery3.png)

5. **DocumentDB 계정** 블레이드에서 **미리 보기 등록**을 클릭합니다. 정보를 읽은 후 **확인**을 클릭합니다.

    ![DocumentDB에 대한 현재 미리 보기 블레이드에 등록 - Azure 포털의 MongoDB에 대한 프로토콜 지원](./media/documentdb-create-mongodb-account/registerforpreview.png)

6.  미리 보기 약관을 수락한 후에 블레이드 만들기로 반환합니다.  **DocumentDB 계정** 블레이드에서 계정에 대한 구성을 원하는 대로 지정합니다.

    ![MongoDB 블레이드에 대한 프로토콜 지원을 사용하는 새 DocumentDB의 스크린샷](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.png)


    - **ID** 상자에 계정을 식별하기 위한 이름을 입력합니다.  **ID**의 유효성이 검사되면 **ID** 상자에 녹색 확인 표시가 나타납니다. **ID** 값은 URI 내의 호스트 이름이 됩니다. **ID** 는 소문자, 숫자 및 '-' 문자만 포함할 수 있으며, 3자에서 50자 사이여야 합니다. 선택한 끝점 이름에 *documents.azure.com* 이 추가되고, 그 결과가 계정 끝점이 됩니다.

    - **구독**에서는 계정에 사용할 Azure 구독을 선택합니다. 계정에 구독이 하나뿐인 경우 해당 계정이 기본적으로 선택됩니다.

    - **리소스 그룹**에서 계정에 대한 리소스 그룹을 선택하거나 만듭니다.  기본적으로 Azure 구독에 따라 기존 리소스 그룹이 선택됩니다.  그러나 계정을 추가할 새로운 리소스 그룹을 만들어 선택할 수 있습니다. 자세한 내용은 [Azure 포털을 사용하여 Azure 리소스 관리](resource-group-portal.md)를 참조하세요.

    - **위치** 를 사용하여 계정을 호스트할 지리적 위치를 지정합니다.
   
    -  **대시보드에 고정** 

7.  새 계정 옵션을 구성하면 **만들기**를 클릭합니다.  계정을 만드는 데 몇 분 정도 걸릴 수 있습니다.  상태를 확인하려면 시작 보드에서 진행률을 모니터링하면 됩니다.  
    ![시작 보드 만들기 타일 - 온라인 데이터베이스 작성자의 스크린샷](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-3.png)  

    그렇지 않은 경우 알림 허브에서 진행률을 모니터링할 수 있습니다.  

    ![신속하게 데이터베이스 만들기 - DocumentDB 계정을 만들고 있음을 보여 주는 알림 허브의 스크린샷](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-4.png)  

    ![DocumentDB 계정을 만들어 리소스 그룹에 배포했음을 보여주는 알림 허브의 스크린샷 - 온라인 데이터베이스 작성자 알림](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-5.png)

8.  계정이 생성되면 기본 설정으로 사용할 수 있습니다. 

    ![기본 계정 블레이드의 스크린샷](./media/documentdb-create-mongodb-account/defaultaccountblades.png)
    

## <a name="next-steps"></a>다음 단계


- MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정을 [연결하는](documentdb-connect-mongodb-account.md) 방법을 알아봅니다.

 



<!--HONumber=Oct16_HO2-->


