---
title: 포함 파일
description: 포함 파일
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: a7e543dcad9ad1b016d1244451cd87cda5ad7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67440200"
---
1. 다음 플랫폼에 대한 클라이언트 SDK 퀵스타트를 다운로드합니다.
    
    [iOS(목표 C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (스위프트)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android(Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [윈도우(C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > iOS 프로젝트를 사용하는 경우 최신\* [GitHub 릴리스에서](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest)"azuresdk-iOS- .zip"을 다운로드해야 합니다. 압축을 `MicrosoftAzureMobile.framework` 풀고 프로젝트의 루트에 파일을 추가합니다.
    >

2. 데이터베이스 연결을 추가하거나 기존 연결에 연결해야 합니다. 먼저 데이터 저장소를 만들지 아니면 기존 저장소를 사용할지 결정합니다.

    - **새 데이터 저장소 만들기:** 데이터 저장소를 만들려면 다음 빠른 시작을 사용합니다.

        [빠른 시작: Azure SQL 데이터베이스에서 단일 데이터베이스로 시작하기](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **기존 데이터 원본**: 기존 데이터베이스 연결을 사용하려면 아래 지침을 따르십시오.

        1. SQL 데이터베이스 연결 문자열 형식 -`Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}** 서버 이름은 데이터베이스의 개요 페이지에서 찾을 수 있으며 일반적으로 "server_name.database.windows.net" 형식입니다.
            **{포트}** 일반적으로 1433.
            **{your_catalogue}** 데이터베이스 의 이름입니다.
            **{your_username}** 데이터베이스에 액세스할 사용자 이름입니다.
            **{your_password}** 데이터베이스에 액세스하는 암호입니다.

            [SQL 연결 문자열 형식에 대해 자세히 알아보기](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. 앱 서비스에서 모바일 **앱에** 연결 문자열을 추가하면 메뉴의 **구성** 옵션을 사용하여 응용 프로그램의 연결 문자열을 관리할 수 있습니다.

            연결 문자열을 추가하려면 다음을 수행하십시오.

            1. **응용 프로그램 설정** 탭을 클릭합니다.

            2. **[+] 새 연결 문자열을**클릭합니다.

            3. 연결 문자열에 **이름,** **값** 및 **유형을** 제공해야 합니다.

            4. **이름**`MS_TableConnectionString`

            5. 값은 이전 단계에서 형성한 연결 문자열이어야 합니다.

            6. SQL Azure 데이터베이스에 연결 문자열을 추가하는 경우 **유형에서** **SQLAzure를** 선택합니다.

3. Azure 모바일 앱에는 .NET 및 Node.js 백엔드에 대한 SDK가 있습니다.

   - **Node.js 백 엔드**
    
     Node.js 퀵스타트 앱을 사용하려면 아래 지침을 따르십시오.

     1. Azure 포털에서 Easy **테이블로**이동하면 이 화면이 표시됩니다.
      
        ![노드 간편테이블](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. SQL 연결 문자열이 **구성** 탭에 이미 추가되어 있는지 확인합니다. 그런 다음 **모든 사이트 내용을 덮어쓰고** **TodoItem 만들기 테이블** 단추를 클릭합니다.
     
        ![노드 간편테이블 구성](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. **쉬운 테이블에서**+ **추가** 버튼을 클릭합니다.
    
        ![노드 쉬운 테이블 추가 버튼](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. 익명 `TodoItem` 액세스 권한이 있는 테이블을 만듭니다.
      
        ![노드 쉬운 테이블 추가 테이블](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **.NET 백 엔드**
    
        .NET 퀵스타트 앱을 사용하려면 아래 지침을 따르세요.

        1. [Azure-mobile-apps-quickstarts 리포지토리에서 Azure Mobile](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart)Apps .NET 서버 프로젝트를 다운로드합니다.

        2. Visual Studio에서 로컬로 .NET 서버 프로젝트를 빌드합니다.

        3. Visual Studio에서 솔루션 탐색기를 열고 `ZUMOAPPNAMEService` 프로젝트를 마우스 오른쪽 단추로 `Publish to App Service` 클릭하고 **게시를**클릭하면 창이 표시됩니다. Mac에서 작업하는 경우 여기에서 앱을 배포하는 다른 방법을 [확인하십시오.](https://docs.microsoft.com/azure/app-service/deploy-local-git)
        
           ![비주얼 스튜디오 퍼블리싱](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. **앱 서비스를** 게시 대상으로 선택한 다음 **기존 선택을**클릭한 다음 창 하단의 **게시** 단추를 클릭합니다.

        5. Azure 구독을 사용하여 먼저 Visual Studio에 로그인해야 합니다. `Subscription`을 `Resource Group`선택한 다음 앱의 이름을 선택합니다. 준비가 되면 **확인을**클릭합니다. 배포가 완료되면 `http://{zumoappname}.azurewebsites.net/` 브라우저에서 리디렉션됩니다.                   