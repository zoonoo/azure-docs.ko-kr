---
title: Azure Cosmos DB용 Visual Studio 연결된 서비스
description: 개발자가 쉽게 해당 Azure Cosmos DB 계정을 연결하고 Visual Studio 연결된 서비스를 통해 리소스를 관리할 수 있습니다.
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jejiang
ms.openlocfilehash: d496d80f1d87b234e0c94333b01ad2c23cc037ab
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/11/2018
---
# <a name="azure-cosmos-db-visual-studio-connected-service-preview"></a>Azure Cosmos DB: Visual Studio 연결된 서비스(미리 보기)

Visual Studio 연결된 서비스를 사용하면 개발자가 쉽게 Azure Cosmos DB 계정을 연결하고 해당 리소스를 관리할 수 있습니다.

또한 서버 쪽 비즈니스 논리를 수행할 수 있도록 연결된 서비스의 데이터 탐색기를 사용하여 저장 프로시저, UDF 및 트리거를 만들 수도 있습니다. 데이터 탐색기는 API에서 사용할 수 있는 모든 기본 제공 프로그래밍 방식 데이터 액세스를 표시하지만 데이터에도 쉽게 액세스할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

다음 항목이 있는지 확인합니다.

* 활성 Azure 계정. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)에 등록할 수 있습니다. 
* Azure Cosmos DB 계정. 아직 계정이 없는 경우 [Azure Cosmos DB 계정 만들기](create-sql-api-dotnet.md)의 단계에 따라 Azure Portal에서 계정을 만들거나 [연결된 서비스 도구에서 Azure Cosmos DB 계정 만들기](#Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool)를 참조하세요. 
* 개발을 위해 로컬 환경을 사용하려는 경우 [Azure Cosmos DB 에뮬레이터](local-emulator.md)를 사용할 수 있습니다. 환경은 Azure Cosmos DB 서비스를 에뮬레이트합니다.
* [Visual Studio](http://www.visualstudio.com/).
* 최신 Azure Cosmos DB 연결된 서비스 비트입니다. 다음 스크린샷에 표시된 대로 Visual Studio Marketplace에서 Azure Cosmos DB 연결된 서비스를 다운로드할 수 있습니다. 컴퓨터에서 **Visual Studio**를 엽니다. **도구** 메뉴에서 **확장 및 업데이트...** 를 선택한 후에 **온라인** / **Visual Studio Marketplace**를 선택합니다. **cosmosdb**를 입력하여 비트를 검색합니다.

    [Visual Studio Marketplace](https://go.microsoft.com/fwlink/?linkid=858709)에서 Azure Cosmos DB 연결된 서비스를 설치할 수도 있습니다.

    ![연결된 서비스 다운로드 bits.png의 스크린샷](./media/connected-service/connected-service-downloadbits.png) 

Azure Cosmos DB 연결된 서비스 확장을 다운로드했으면 Visual Studio를 닫고 확장을 설치합니다.

## <a id="SetupVS"></a>Visual Studio 솔루션 설치
1. 컴퓨터에서 **Visual Studio**를 엽니다.
2. **파일** 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 선택합니다.
3. **새 프로젝트** 대화 상자에서 **Visual C#** / **콘솔 앱(.NET Framework)** 또는 **WPF 앱(.NET Framework)**을 선택하고, 프로젝트 이름을 지정한 다음, **확인**을 클릭합니다.

    ![새 프로젝트 창의 스크린샷](./media/connected-service/connected-service-new-project.png)
    
## <a name="add-connected-service-and-add-account"></a>연결된 서비스 추가 및 계정 추가
1. 솔루션 탐색기에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **추가** / **연결된 서비스**를 선택합니다. **프로젝트** 메뉴를 클릭하고 **연결된 서비스 추가**를 선택합니다.

    ![연결된 서비스 추가 창의 스크린샷](./media/connected-service/connected-service-add-connectedservice-rightclick.png)
2. 연결된 서비스 페이지에서 **연결된 서비스** / **Azure Cosmos DB**를 클릭하여 **Azure Cosmos DB** 페이지를 엽니다.

    ![Azure Cosmos DB 창의 스크린샷](./media/connected-service/connected-service-choose-azure-cosmosdb.png)
3. 처음으로 로그인하거나 계정을 추가하려면 아래쪽 화살표를 클릭합니다. 로그인한 후에 모든 Azure Cosmos DB 계정이 빈 영역에 표시됩니다. Azure Cosmos DB 계정을 선택하여 프로젝트에 추가합니다.

    ![로그인 및 나열된 DB 계정 창의 스크린샷](./media/connected-service/connected-service-add-db-account.png)
4. Azure Cosmos DB 계정을 추가하면 Azure Cosmos DB 계정 연결된 서비스 폴더가 프로젝트에 추가됩니다. 1단계~3단계를 반복하여 하나 이상의 Azure Cosmos DB 계정을 추가할 수 있습니다.

    ![연결된 서비스 폴더 창의 스크린샷](./media/connected-service/connected-service-add-connectedservice-folder.png)

5. Azure Cosmos DB 연결된 서비스를 추가하면 다음 방법 중 하나로 Azure Cosmos DB에 액세스할 수 있도록 프로젝트를 수정합니다.

    * Azure Cosmos DB 클라이언트에 필요한 일부 NuGet 패키지가 설치됩니다. 패키지 구성 파일에서 확인할 수 있습니다. 

        ![새 Azure Cosmos DB 패키지 구성](./media/connected-service/connected-service-packages-config.png)   
    
    * Azure Cosmos DB 연결 URI 및 키가 프로젝트 구성 파일에 추가됩니다. 이 예제에서는 App.config입니다. 

        ![새 Azure Cosmos DB 앱 구성](./media/connected-service/connected-service-app-config.png) 

## <a name="open-azure-cosmos-db-explorer"></a>Azure Cosmos DB 탐색기 열기
1. 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **Cosmos DB 탐색기 열기**를 선택합니다.

    ![데이터 탐색기 열기 창에 사용하는 스크린샷](./media/connected-service/connected-service-right-click-open-data-exporer.png)
2. **Cosmos DB 계정 선택** 페이지에서 드롭다운 목록을 클릭하여 Azure Cosmos DB 계정 하나를 선택합니다.

    ![계정 연결된 서비스 추가 창의 스크린샷](./media/connected-service/connected-service-open-explorer.png)
3. **열기**를 클릭하면 데이터 탐색기 창이 표시됩니다.

## <a id="Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool"></a>연결된 서비스 도구에서 Azure Cosmos DB 계정 만들기
1. 연결된 서비스 페이지 창의 왼쪽 아래에서 **새 Cosmos DB 계정 만들기**를 클릭하여 **Cosmos DB 계정 만들기** 페이지를 엽니다.

    ![Azure Cosmos DB 계정 만들기 진입점 창 열기의 스크린샷](./media/connected-service/connected-service-click-new-db-account.png)
2. **Cosmos DB 계정 만들기** 페이지에서 이 Azure Cosmos DB 계정에 원하는 구성을 지정합니다.

    다음 스크린샷의 정보를 참조로 사용하여 **Cosmos DB 계정 만들기** 페이지의 필드를 완성합니다. 
 
    ![새 Azure Cosmos DB 페이지](./media/connected-service/connected-service-create-new-account.png)        
3. **만들기**를 클릭하여 계정을 만듭니다.

## <a name="use-data-explorer"></a>데이터 탐색기 사용

데이터 탐색기를 연 후에 다음을 수행할 수 있습니다.
* 데이터베이스 만들기 및 삭제
* 컬렉션 만들기 및 삭제
* 문서 만들기, 삭제 및 필터링
* 저장된 프로시저 만들기 및 삭제
* 서버 쪽 비즈니스 논리를 수행하기 위해 트리거 및 사용자 정의 함수를 만들고 삭제합니다. 

![새 Azure Cosmos DB 페이지](./media/connected-service/connected-service-dataexplorerui.png)

## <a name="demo"></a>데모

Visual Studio에서 Azure Cosmos DB 연결된 서비스를 사용하는 방법을 보려면 [Visual Studio에서 Azure Cosmos DB 연결된 서비스 사용](https://go.microsoft.com/fwlink/?linkid=858711) 비디오를 시청하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 다음 항목에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure Cosmos DB 계정 만들기
> * 연결된 서비스 추가 및 계정 추가
> * Azure Cosmos DB 탐색기 열기
> * 데이터 탐색기 사용

이제 연결된 서비스를 설치하고 Azure Cosmos DB 계정을 사용하여 실행하고 있으므로 솔루션 개발을 시작하려면 자습서 중 하나를 계속 진행합니다.

* [.NET의 SQL API를 사용하여 개발](tutorial-develop-sql-api-dotnet.md)
* [Azure Cosmos DB: SQL API 시작 자습서](sql-api-get-started.md)
* Azure Cosmos DB를 사용하여 규모 및 성능 테스트를 수행하고 싶으신가요? [Azure Cosmos DB를 사용한 성능 및 규모 테스트](performance-testing.md)를 참조하세요.
* [Azure Cosmos DB 계정 모니터링](monitor-accounts.md) 방법에 대해 알아보세요.

