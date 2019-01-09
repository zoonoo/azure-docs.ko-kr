---
title: .NET Core 콘솔 앱 빌드로 Azure Cosmos DB SQL API 계정의 데이터 관리(SDK 버전 3 미리 보기)
description: Azure Cosmos DB SQL API .NET Core SDK를 사용하여 온라인 데이터베이스 및 C# 콘솔 응용 프로그램을 만드는 자습서입니다.
author: deborahc
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/01/2018
ms.author: dech
ms.openlocfilehash: 917cf6ddf23fb9240aa6f2cef8add14c66d0cb06
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53973176"
---
# <a name="build-a-net-core-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account-sdk-version-3-preview"></a>.NET Core 콘솔 앱 빌드로 Azure Cosmos DB SQL API 계정의 데이터 관리(SDK 버전 3 미리 보기)

> [!div class="op_single_selector"]
>
> - [.NET Core(미리 보기)](sql-api-dotnet-core-get-started-preview.md)
> - [.NET Core](sql-api-dotnetcore-get-started.md)
> - [.NET(미리 보기)](sql-api-dotnet-get-started-preview.md)
> - [.NET](sql-api-get-started.md)
> - [Java](sql-api-java-get-started.md)
> - [비동기 Java](sql-api-async-java-get-started.md)
> - [Node.JS](sql-api-nodejs-get-started.md)

.NET Core 자습서부터 시작하여 Azure Cosmos DB SQL API를 살펴보겠습니다. 이 자습서를 따라 하면 Azure Cosmos DB 리소스를 만들고 쿼리하는 .NET Core 콘솔 애플리케이션을 만들 수 있습니다. 이 자습서에서는 [.NET 표준 2.0](https://docs.microsoft.com/dotnet/standard/net-standard)을 대상으로 하는 [버전 3.0 이상](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) Azure Cosmos DB .NET SDK를 사용합니다.

이 자습서에서는 다음 내용을 다룹니다.

> [!div class="checklist"]
>
> - Azure Cosmos 계정 만들기 및 연결
> - Visual Studio에서 프로젝트 구성
> - 데이터베이스 및 컨테이너 만들기
> - 컨테이너에 항목 추가
> - 컨테이너 쿼리
> - 항목에 대한 CRUD 작업
> - 데이터베이스 삭제

응용 프로그램을 만든 시간이 없나요? 염려하지 마십시오. [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started)에서 전체 솔루션을 사용할 수 있습니다. 빠른 지침을 보려면 [전체 솔루션 다운로드 섹션](#GetSolution) 으로 이동하세요.

SQL API 및 .NET Core SDK를 사용하여 Xamarin iOS, Android 또는 Forms 응용 프로그램을 빌드하시겠어요? [Xamarin 및 Azure Cosmos DB를 사용하여 모바일 응용 프로그램 빌드](mobile-apps-with-xamarin.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

- 활성 Azure 계정. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)에 등록할 수 있습니다.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

- Visual Studio 2017이 아직 설치되지 않은 경우 무료 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. UWP(유니버설 Windows 플랫폼) 앱을 개발하는 경우에는 **Visual Studio 2017 버전 15.4** 이상을 사용해야 합니다. Visual Studio를 설치하는 동안 **Azure 개발** 워크로드를 사용하도록 설정해야 합니다.

  - MacOS 또는 Linux에서 작업하는 경우 원하는 플랫폼에 대한 [.NET Core SDK](https://www.microsoft.com/net/core#macos)를 설치하여 명령줄에서 .NET Core 앱을 개발할 수 있습니다.

  - Windows에서 작업하는 경우 [.NET Core SDK](https://www.microsoft.com/net/core#windows)를 설치하여 명령줄에서 .NET Core 앱을 개발할 수 있습니다.

  - 자체 편집기를 사용하거나 Windows, Linux, MacOS에서 작동하는 무료 [Visual Studio Code](https://code.visualstudio.com/)를 다운로드할 수 있습니다.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1단계: Azure Cosmos DB 계정 만들기

Azure Cosmos DB 계정을 만들어 보겠습니다. 계정이 이미 있는 경우 [Visual Studio 솔루션 설치](#SetupVS)로 건너뛸 수 있습니다. Azure Cosmos DB 에뮬레이터를 사용하는 경우 [Azure Cosmos DB 에뮬레이터](local-emulator.md)의 단계에 따라 에뮬레이터를 설치하고 [Visual Studio 프로젝트 설치](#SetupVS)로 건너뜁니다.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>2단계: Visual Studio 프로젝트 설정

1. 컴퓨터에서 **Visual Studio 2017**을 엽니다.
1. **파일** 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 선택합니다.
1. **새 프로젝트** 대화 상자에서 **Visual C#** / **콘솔 앱(.NET Core)** 을 선택하고, 프로젝트 이름을 지정한 다음, **확인**을 클릭합니다.
   ![새 프로젝트 창의 스크린샷](./media/sql-api-dotnetcore-get-started/dotnetcore-tutorial-visual-studio-new-project.png)
1. **솔루션 탐색기**에서 Visual Studio 솔루션 아래에 있는 새 콘솔 응용 프로그램을 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리...** 를 클릭합니다.

   ![프로젝트의 마우스 오른쪽 단추 클릭 메뉴의 스크린샷](./media/sql-api-dotnetcore-get-started/dotnetcore-tutorial-visual-studio-manage-nuget.png)

1. **NuGet** 탭에서 **찾아보기**를 클릭하고, 검색 상자에 **Microsoft.Azure.Cosmos**를 입력합니다.
1. 결과 내에서 **Microsoft.Azure.Cosmos**를 찾고 **설치**를 클릭합니다.
   Azure Cosmos DB SQL API 클라이언트 라이브러리의 패키지 ID는 [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)입니다.
   ![Azure Cosmos DB 클라이언트 SDK를 찾기 위한 NuGet 메뉴의 스크린샷](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

   솔루션 변경 내용을 검토하는 메시지가 표시되면 **확인**을 클릭합니다. 라이선스 승인에 관한 메시지가 표시되면 **동의합니다.** 를 클릭합니다.

잘하셨습니다. 설치를 완료했으므로 코드를 작성해 보겠습니다. [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started/)에서 이 자습서의 완성된 코드 프로젝트를 찾을 수 있습니다.

## <a id="Connect"></a>3단계: Azure Cosmos DB 계정에 연결

1. 먼저 **Program.cs** 파일에서 C# 애플리케이션의 시작 부분에 있는 참조를 다음 참조로 바꿉니다.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;
   using System.Collections.Generic;
   using System.Net;
   ```

1. 이제 공용 클래스 `Program`에 이러한 상수 및 변수를 추가합니다.

   ```csharp
   public class Program
   {
       // ADD THIS PART TO YOUR CODE

       // The Azure Cosmos DB endpoint for running this sample.
       private static readonly string EndpointUri = "<your endpoint here>";
       // The primary key for the Azure Cosmos account.
       private static readonly string PrimaryKey = "<your primary key>";

       // The Cosmos client instance
       private CosmosClient cosmosClient;

       // The database we will create
       private CosmosDatabase database;

       // The container we will create.
       private CosmosContainer container;

       // The name of the database and container we will create
       private string databaseId = "FamilyDatabase";
       private string containerId = "FamilyContainer";
   }
   ```

   이전 버전의 .NET SDK에 익숙한 경우 '컬렉션' 및 '문서'라는 용어를 자주 들어 보셨을 것입니다. Azure Cosmos DB는 여러 API 모델을 지원하므로 .NET SDK 버전 3.0 이상에서는 일반 용어 '컨테이너' 및 '항목'을 사용합니다. 컨테이너는 컬렉션, 그래프 또는 테이블입니다. 항목은 문서, 에지/꼭짓점 또는 행이며, 컨테이너 내부의 콘텐츠입니다. [데이터베이스, 컨테이너 및 항목에 대해 알아봅니다.](databases-containers-items.md)

1. [Azure Portal](https://portal.azure.com)에서 엔드포인트 URL과 기본 키를 검색합니다.

   Azure Portal에서 Azure Cosmos DB 계정으로 이동한 다음 **키**를 클릭합니다.

   포털에서 URI를 복사하고 `Program.cs` 파일의 `<your endpoint URL>`에 붙여넣습니다. 포털에서 기본 키를 복사하고 `<your primary key>`에 붙여넣습니다.

   ![Azure Portal에서 Azure Cosmso DB 키를 가져오는 스크린샷](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. 다음으로, `CosmosClient`의 새 인스턴스를 만들고 프로그램에 대한 몇 가지 스캐폴딩을 설정합니다.

   **Main** 메서드 아래에 **GetStartedDemoAsync**라는 이름의 새 비동기 작업을 추가하면, 새 `CosmosClient`가 인스턴스화됩니다. **GetStartedDemoAsync**를 Azure Cosmos DB 리소스에서 작업하는 메서드를 호출하는 진입점으로 사용합니다.

   ```csharp
   public static async Task Main(string[] args)
   {
   }

   // ADD THIS PART TO YOUR CODE
   /*
       Entry point to call methods that operate on Azure Cosmos DB resources in this sample
   */
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
   }
   ```

1. 다음 코드를 추가하여 **Main** 메서드에서 **GetStartedDemoAsync** 비동기 작업을 실행합니다. **Main** 메서드가 예외를 catch하여 콘솔에 기록합니다.

   ```csharp
   public static async Task Main(string[] args)
   {
       // ADD THIS PART TO YOUR CODE
       try
       {
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();
       }
       catch (CosmosException de)
       {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
       }
       catch (Exception e)
       {
           Console.WriteLine("Error: {0}\n", e);
       }
       finally
       {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
       }
   }
   ```

1. **F5** 키를 선택하여 애플리케이션을 실행합니다. 콘솔 창 출력에서는 Azure Cosmos DB에 연결되었음을 확인하는 `End of demo, press any key to exit.` 메시지가 표시됩니다. 그런 다음 콘솔 창을 닫을 수 있습니다.

축하합니다! Azure Cosmos DB 계정에 성공적으로 연결되었습니다.

## <a name="step-4-create-a-database"></a>4단계: 데이터베이스 만들기

**CosmosDatabases** 클래스의 [**CreateDatabaseIfNotExistsAsync**](https://aka.ms/CosmosDotnetAPIDocs) 또는 [**CreateDatabaseAsync**](https://aka.ms/CosmosDotnetAPIDocs) 함수를 사용하여 데이터베이스를 만들 수 있습니다. 데이터베이스는 여러 컨테이너에 분할된 항목의 논리적 컨테이너입니다.

1. **CreateDatabase** 메서드를 **GetStartedDemoAsync** 메서드 아래에 복사하여 붙여넣습니다. **CreateDatabase**는 ID가 `FamilyDatabase`인 새 데이터베이스를 만들며, 아직 없는 경우 `databaseId` 필드에서 ID가 지정됩니다.

   ```csharp
   /*
       Create the database if it does not exist
   */
   private async Task CreateDatabase()
   {
       // Create a new database
       this.database = await this.cosmosClient.Databases.CreateDatabaseIfNotExistsAsync(databaseId);
       Console.WriteLine("Created Database: {0}\n", this.database.Id);
   }
   ```

1. CosmosClient를 인스턴스화한 아래 코드를 복사하여 붙여넣어 방금 추가한 **CreateDatabase** 메서드를 호출합니다.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

       //ADD THIS PART TO YOUR CODE
       await this.CreateDatabase();
   }
   ```

   이 시점에서 엔드포인트 및 기본 키를 입력한 코드는 다음과 같아야 합니다. 네임스페이스는 프로젝트의 이름을 기준으로 달라집니다.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;
   using System.Collections.Generic;
   using System.Net;

   namespace CosmosGettingStartedDotnetCoreTutorial
   {
       class Program
       {
           // The Azure Cosmos DB endpoint for running this sample.
           private static readonly string EndpointUri = "<your endpoint here>";
           // The primary key for the Azure Cosmos account.
           private static readonly string PrimaryKey = "<your primary key>";

           // The Cosmos client instance
           private CosmosClient cosmosClient;

           // The database we will create
           private CosmosDatabase database;

           // The container we will create.
           private CosmosContainer container;

           // The name of the database and container we will create
           private string databaseId = "FamilyDatabase";
           private string containerId = "FamilyContainer";

           public static async Task Main(string[] args)
           {
               try
               {
                   Console.WriteLine("Beginning operations...");
                   Program p = new Program();
                   await p.GetStartedDemoAsync();
               }
               catch (CosmosException de)
               {
                   Exception baseException = de.GetBaseException();
                   Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
               }
               catch (Exception e)
               {
                   Console.WriteLine("Error: {0}\n", e);
               }
               finally
               {
                   Console.WriteLine("End of demo, press any key to exit.");
                   Console.ReadKey();
               }
           }

           /*
               Entry point to call methods that operate on Azure Cosmos DB resources in this sample
           */
           public async Task GetStartedDemoAsync()
           {
               // Create a new instance of the Cosmos Client
               this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
               await this.CreateDatabase();
           }

           /*
               Create the database if it does not exist
           */
           private async Task CreateDatabase()
           {
               // Create a new database
               this.database = await this.cosmosClient.Databases.CreateDatabaseIfNotExistsAsync(databaseId);
               Console.WriteLine("Created Database: {0}\n", this.database.Id);
           }
       }
   }
   ```

**F5** 키를 선택하여 애플리케이션을 실행합니다.

축하합니다! Azure Cosmos DB 데이터베이스를 성공적으로 만들었습니다.

## <a id="CreateColl"></a>5단계: 컨테이너 만들기

> [!WARNING]
> **CreateContainerIfNotExistsAsync** 메서드를 호출하면 가격 책정에 영향을 미치는 새 컨테이너가 생성됩니다. 자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.

**CosmosContainers** 클래스의 [**CreateContainerIfNotExistsAsync**](https://aka.ms/CosmosDotnetAPIDocs) 또는 [**CreateContainerAsync**](https://aka.ms/CosmosDotnetAPIDocs) 함수를 사용하여 컨테이너를 만들 수 있습니다. 컨테이너는 항목(SQL API의 경우 JSON 문서) 및 관련 JavaScript 서버 쪽 애플리케이션 논리(예: 저장 프로시저, 사용자 정의 함수 및 트리거)로 구성됩니다.

1. **CreateContainer** 메서드를 **CreateDatabase** 메서드 아래에 복사하여 붙여넣습니다. **CreateContainer**는 ID가 `FamilyContainer`인 새 컨테이너를 만들며, 아직 없는 경우 `containerId` 필드에서 ID가 지정됩니다.

   ```csharp
   /*
       Create the container if it does not exist.
       Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
   */
   private async Task CreateContainer()
   {
       // Create a new container
       this.container = await this.database.Containers.CreateContainerIfNotExistsAsync(containerId, "/LastName");
       Console.WriteLine("Created Container: {0}\n", this.container.Id);
   }
   ```

1. CosmosClient를 인스턴스화한 아래 코드를 복사하여 붙여넣어 방금 추가한 **CreateContainer** 메서드를 호출합니다.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();

       //ADD THIS PART TO YOUR CODE
       await this.CreateContainer();
   }
   ```

**F5** 키를 선택하여 애플리케이션을 실행합니다.

축하합니다! Azure Cosmos DB 컨테이너를 성공적으로 만들었습니다.

## <a id="CreateDoc"></a>6단계: 컨테이너에 항목 추가

항목은 **CosmosItems** 클래스의 [**CreateItemAsync**](https://aka.ms/CosmosDotnetAPIDocs) 함수를 사용하여 만들 수 있습니다. SQL API를 사용하는 경우 항목은 문서로 보호되며, 사용자 정의(임의) JSON 콘텐츠입니다. 이제 Azure Cosmos DB 컨테이너에 항목을 삽입할 수 있습니다.

먼저 이 샘플에서는 Azure Cosmos DB 내에 저장된 개체를 나타내는 **가족** 클래스를 만들어야 합니다. 또한 **가족** 내에서 사용되는 **부모**, **자식**, **애완 동물**, **주소** 하위 클래스를 만듭니다. 문서에는 JSON에서 **ID**로 직렬화된 **ID** 속성이 있어야 합니다.

1.  **Ctrl+Shift+A**를 선택하여 **새 항목 추가** 대화 상자를 엽니다. 프로젝트에 새 클래스인 **Family.cs**를 추가합니다.

    ![프로젝트에 새 Family.cs 클래스를 추가하는 스크린샷](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1.  **가족**, **부모**, **자식**, **애완 동물** 및 **주소** 클래스를 복사하여 **Family.cs**에 붙여넣습니다. 네임스페이스는 프로젝트의 이름을 기준으로 달라집니다.

    ```csharp
    using Newtonsoft.Json;

    namespace CosmosGettingStartedDotnetCoreTutorial
    {
        public class Family
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
            public string LastName { get; set; }
            public Parent[] Parents { get; set; }
            public Child[] Children { get; set; }
            public Address Address { get; set; }
            public bool IsRegistered { get; set; }
            public override string ToString()
            {
                return JsonConvert.SerializeObject(this);
            }
        }

        public class Parent
        {
            public string FamilyName { get; set; }
            public string FirstName { get; set; }
        }

        public class Child
        {
            public string FamilyName { get; set; }
            public string FirstName { get; set; }
            public string Gender { get; set; }
            public int Grade { get; set; }
            public Pet[] Pets { get; set; }
        }

        public class Pet
        {
            public string GivenName { get; set; }
        }

        public class Address
        {
            public string State { get; set; }
            public string County { get; set; }
            public string City { get; set; }
        }
    }
    ```

1.  **Program.cs**로 돌아가서 **CreateContainer** 메서드 아래에 **AddItemsToContainer** 메서드를 추가합니다.
    코드는 ID를 만들기 전에 같은 ID를 가진 항목이 있는지 확인합니다. Andersen Family와 Wakefield Family의 항목 하나씩 두 개의 항목을 삽입하겠습니다.

    ```csharp
     /*
         Add Family items to the container
     */
     private async Task AddItemsToContainer()
     {
         // Create a family object for the Andersen family
         Family andersenFamily = new Family
         {
             Id = "Andersen.1",
             LastName = "Andersen",
             Parents = new Parent[]
             {
                 new Parent { FirstName = "Thomas" },
                 new Parent { FirstName = "Mary Kay" }
             },
             Children = new Child[]
             {
                 new Child
                 {
                     FirstName = "Henriette Thaulow",
                     Gender = "female",
                     Grade = 5,
                     Pets = new Pet[]
                     {
                         new Pet { GivenName = "Fluffy" }
                     }
                 }
             },
             Address = new Address { State = "WA", County = "King", City = "Seattle" },
             IsRegistered = true
         };

         // Read the item to see if it exists. Note ReadItemAsync will not throw an exception if an item does not exist. Instead, we check the StatusCode property off the response object.
         CosmosItemResponse<Family> andersenFamilyResponse = await this.container.Items.ReadItemAsync<Family>(andersenFamily.LastName, andersenFamily.Id);

         if (andersenFamilyResponse.StatusCode == HttpStatusCode.NotFound)
         {
             // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen"
             andersenFamilyResponse = await this.container.Items.CreateItemAsync<Family>(andersenFamily.LastName, andersenFamily);

             // Note that after creating the item, we can access the body of the item with the Resource property off the CosmosItemResponse.
             //We can also access the RequestCharge property to see the amount of RUs consumed on this request.
             Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
         }
         else
         {
             Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamilyResponse.Resource.Id);
         }

         // Create a family object for the Wakefield family
         Family wakefieldFamily = new Family
         {
             Id = "Wakefield.7",
             LastName = "Wakefield",
             Parents = new Parent[]
             {
                 new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
                 new Parent { FamilyName = "Miller", FirstName = "Ben" }
             },
             Children = new Child[]
             {
                 new Child
                 {
                     FamilyName = "Merriam",
                     FirstName = "Jesse",
                     Gender = "female",
                     Grade = 8,
                     Pets = new Pet[]
                     {
                         new Pet { GivenName = "Goofy" },
                         new Pet { GivenName = "Shadow" }
                     }
                 },
                 new Child
                 {
                     FamilyName = "Miller",
                     FirstName = "Lisa",
                     Gender = "female",
                     Grade = 1
                 }
             },
             Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
             IsRegistered = false
         };

         // Read the item to see if it exists
         CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.ReadItemAsync<Family>(wakefieldFamily.LastName, wakefieldFamily.Id);

         if (wakefieldFamilyResponse.StatusCode == HttpStatusCode.NotFound)
         {
             // Create an item in the container representing the Wakefield family. Note we provide the value of the partition key for this item, which is "Wakefield"
             wakefieldFamilyResponse = await this.container.Items.CreateItemAsync<Family>(wakefieldFamily.LastName, wakefieldFamily);

             // Note that after creating the item, we can access the body of the item with the Resource property off the CosmosItemResponse.
             //We can also access the RequestCharge property to see the amount of RUs consumed on this request.
             Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", wakefieldFamilyResponse.Resource.Id, wakefieldFamilyResponse.RequestCharge);
         }
         else
         {
             Console.WriteLine("Item in database with id: {0} already exists\n", wakefieldFamilyResponse.Resource.Id);
         }
     }
    
    ```

1.  호출을 `GetStartedDemoAsync` 메서드의 `AddItemsToContainer`에 추가합니다.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabase();
        await this.CreateContainer();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainer();
    }
    ```

**F5** 키를 선택하여 애플리케이션을 실행합니다.

축하합니다! 두 개의 Azure Cosmos DB 항목을 성공적으로 만들었습니다.

## <a id="Query"></a>7단계: Azure Cosmos DB 리소스 쿼리

Azure Cosmos DB는 각 컬렉션에 저장된 JSON 문서에 대해 [다양한 쿼리](sql-api-sql-query.md)를 지원합니다. 다음 샘플 코드에서는 이전 단계에서 삽입한 항목에 대해 쿼리를 실행하는 방법을 보여줍니다.

1. **RunQuery** 메서드를 **AddItemsToContainer** 메서드 아래에 복사하여 붙여넣습니다.

   ```csharp
   /*
       Run a query (using Azure Cosmos DB SQL syntax) against the container
   */
   private async Task RunQuery()
   {
       var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";
       var partitionKeyValue = "Andersen";

       Console.WriteLine("Running query: {0}\n", sqlQueryText);

       CosmosSqlQueryDefinition queryDefinition = new CosmosSqlQueryDefinition(sqlQueryText);
       CosmosResultSetIterator<Family> queryResultSetIterator = this.container.Items.CreateItemQuery<Family>(queryDefinition, partitionKeyValue);

       List<Family> families = new List<Family>();

       while (queryResultSetIterator.HasMoreResults)
       {
           CosmosQueryResponse<Family> currentResultSet = await queryResultSetIterator.FetchNextSetAsync();
           foreach (Family family in currentResultSet)
           {
               families.Add(family);
               Console.WriteLine("\tRead {0}\n", family);
           }
       }
   }
   ```

1. 호출을 `GetStartedDemoAsync` 메서드의 `RunQuery`에 추가합니다.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();

       //ADD THIS PART TO YOUR CODE
       await this.RunQuery();
   }
   ```

**F5** 키를 선택하여 애플리케이션을 실행합니다.

축하합니다! Azure Cosmos DB 컨테이너에 대한 쿼리가 성공적으로 수행되었습니다.

## <a id="ReplaceItem"></a>8단계: JSON 항목 바꾸기

이제 Azure Cosmos DB에서 항목을 업데이트하겠습니다.

1. **ReplaceFamilyItem** 메서드를 **RunQuery** 메서드 아래에 복사하여 붙여넣습니다. 제품군의 `IsRegistered` 속성 및 자식 중 하나인 `Grade`를 변경하는 것입니다.

   ```csharp
   /*
   Update an item in the container
   */
   private async Task ReplaceFamilyItem()
   {
       CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.ReadItemAsync<Family>("Wakefield", "Wakefield.7");
       var itemBody = wakefieldFamilyResponse.Resource;

       // update registration status from false to true
       itemBody.IsRegistered = true;
       // update grade of child
       itemBody.Children[0].Grade = 6;

       // replace the item with the updated content
       wakefieldFamilyResponse = await this.container.Items.ReplaceItemAsync<Family>(itemBody.LastName, itemBody.Id, itemBody);
       Console.WriteLine("Updated Family [{0},{1}]\n. Body is now: {2}\n", itemBody.LastName, itemBody.Id, wakefieldFamilyResponse.Resource);
   }
   ```

1. 호출을 `GetStartedDemo` 메서드의 `ReplaceFamilyItem`에 추가합니다.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();

       //ADD THIS PART TO YOUR CODE
       await this.ReplaceFamilyItem();
   }
   ```

**F5** 키를 선택하여 애플리케이션을 실행합니다.

축하합니다! Azure Cosmos DB 항목을 성공적으로 대체했습니다.

## <a id="DeleteDocument"></a>9단계: 항목 삭제

이제 Azure Cosmos DB에서 항목을 삭제하겠습니다.

1. **DeleteFamilyItem** 메서드를 복사하여 **ReplaceFamilyItem** 메서드 아래에 붙여넣습니다.

   ```csharp
   /*
   Delete an item in the container
   */
   private async Task DeleteFamilyItem()
   {
       var partitionKeyValue = "Wakefield";
       var familyId = "Wakefield.7";

       // Delete an item. Note we must provide the partition key value and id of the item to delete
       CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.DeleteItemAsync<Family>(partitionKeyValue, familyId);
       Console.WriteLine("Deleted Family [{0},{1}]\n", partitionKeyValue, familyId);
   }
   ```

1. 호출을 `GetStartedDemo` 메서드의 `DeleteFamilyItem`에 추가합니다.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();
       await this.ReplaceFamilyItem();

       //ADD THIS PART TO YOUR CODE
       await this.DeleteFamilyItem();
   }
   ```

**F5** 키를 선택하여 애플리케이션을 실행합니다.

축하합니다! Azure Cosmos DB 항목을 성공적으로 삭제했습니다.

## <a id="DeleteDatabase"></a>10단계: 데이터베이스 삭제

이제 데이터베이스를 삭제하겠습니다. 만든 데이터베이스를 삭제하면 데이터베이스와 모든 자식 리소스(컨테이너, 항목, 저장 프로시저, 사용자 정의 함수 및 트리거)가 제거됩니다. 또한 **CosmosClient** 인스턴스도 삭제하겠습니다.

1. **DeleteDatabaseAndCleanup** 메서드를 **DeleteFamilyItem** 메서드 아래에 복사하여 붙여넣습니다.

   ```csharp
   /*
   Delete the database and dispose of the Cosmos Client instance
   */
   private async Task DeleteDatabaseAndCleanup()
   {
       CosmosDatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
       // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

       Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

       //Dispose of CosmosClient
       this.cosmosClient.Dispose();
   }
   ```

1. 호출을 `GetStartedDemo` 메서드의 `DeleteDatabaseAndCleanup`에 추가합니다.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();
       await this.ReplaceFamilyItem();
       await this.DeleteFamilyItem();

       //ADD THIS PART TO YOUR CODE
       await this.DeleteDatabaseAndCleanup();
   }
   ```

**F5** 키를 선택하여 애플리케이션을 실행합니다.

축하합니다! Azure Cosmos DB 데이터베이스를 성공적으로 삭제했습니다.

## <a id="Run"></a>11단계: C# 콘솔 애플리케이션 모두 함께 실행

디버그 모드에서 애플리케이션을 빌드하려면 Visual Studio에서 **F5**를 선택합니다.

시작한 전체 앱의 출력이 콘솔 창에 표시됩니다. 출력은 추가한 쿼리 결과를 보여 주며, 아래 예제 텍스트와 일치해야 합니다.

```
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

축하합니다! 이 자습서를 완료했으며 실행되는 C# 콘솔 애플리케이션이 생겼습니다.

## <a id="GetSolution"></a> 전체 자습서 솔루션 가져오기

이 문서의 모든 샘플을 포함하는 GetStarted 솔루션을 빌드하려면 다음이 필요합니다.

- 활성 Azure 계정. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)에 등록할 수 있습니다.
- [Azure Cosmos DB 계정][cosmos-db-create-account].
- GitHub에서 제공하는 [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started) 솔루션

Visual Studio에서 Azure Cosmos DB .NET Core SDK용 SQL API에 대한 참조를 복원하려면 솔루션 탐색기에서 **GetStarted** 솔루션을 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 복원**을 선택합니다. 다음으로, **Program.cs** 파일에서 `EndpointUri` 및 `PrimaryKey` 값을 [Azure Cosmos DB 계정에 연결](#Connect)에 설명된 대로 업데이트합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Cosmos DB SQL API 데이터를 관리하는 .NET Core 앱을 빌드하는 방법을 알아보았습니다. 이제 사용자의 Cosmos DB 계정에 추가 데이터를 가져올 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 데이터 가져오기](import-data.md)

[cosmos-db-create-account]: create-sql-api-dotnet-preview.md#create-account
