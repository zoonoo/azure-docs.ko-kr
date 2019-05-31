---
title: Azure Cosmos DB 에뮬레이터 빌드 작업을 사용하여 CI/CD 파이프라인 설정
description: Cosmos DB 에뮬레이터 빌드 작업을 사용하여 Azure DevOps에서 빌드 및 릴리스 워크플로를 설정하는 방법에 대한 자습서
author: deborahc
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: b97fb1956c75332c40e242484b9d94419a45eb92
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242552"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>Azure Cosmos DB 에뮬레이터 빌드 작업을 사용하여 Azure DevOps에서 CI/CD 파이프라인 설정

Azure Cosmos DB 에뮬레이터는 개발 목적으로 Azure Cosmos DB 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. 이 에뮬레이터를 사용하면 Azure 구독을 구입하거나 비용을 발생시키지 않고도 로컬에서 응용 프로그램을 테스트할 수 있습니다. 

Azure DevOps에 대한 Azure Cosmos DB 에뮬레이터 빌드 작업을 사용하면 CI 환경에서와 동일한 작업을 수행할 수 있습니다. 빌드 작업을 사용하면 빌드 및 릴리스 워크플로의 일부로 에뮬레이터에 대한 테스트를 실행할 수 있습니다. 이 작업은 에뮬레이터가 이미 실행 중인 Docker 컨테이너를 스핀업하고, 빌드 정의의 나머지 부분에서 사용할 수 있는 엔드포인트를 제공합니다. 별도 컨테이너에서 실행되는 필요한 만큼 많은 에뮬레이터의 인스턴스를 만들고 시작할 수 있습니다. 

이 문서에서는 Cosmos DB 에뮬레이터 빌드 작업을 사용하여 테스트를 실행하는 ASP.NET 응용 프로그램에 대해 Azure DevOps에서 CI 파이프라인을 설정하는 방법을 설명합니다. 유사한 방법을 사용하여 Node.js 또는 Python 애플리케이션에 대한 CI 파이프라인을 설정할 수 있습니다. 

## <a name="install-the-emulator-build-task"></a>에뮬레이터 빌드 작업 설치

빌드 작업을 사용하려면 먼저 Azure DevOps 조직에 설치해야 합니다. [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview)에서 **Azure Cosmos DB 에뮬레이터** 확장을 찾고, **무료 다운로드**를 클릭합니다.

![Azure DevOps Marketplace에서 Azure Cosmos DB 에뮬레이터 빌드 작업 찾기 및 설치](./media/tutorial-setup-ci-cd/addExtension_1.png)

다음으로, 확장을 설치할 조직을 선택합니다. 

> [!NOTE]
> Azure DevOps 조직에 확장을 설치하려면 계정 소유자 또는 프로젝트 컬렉션 관리자여야 합니다. 사용 권한이 없지만 계정 멤버인 경우 대신 확장을 요청할 수 있습니다. [자세한 정보](https://docs.microsoft.com/azure/devops/marketplace/faq-extensions?view=vsts#install-request-assign-and-access-extensions)

![확장을 설치할 Azure DevOps 조직을 선택합니다.](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>빌드 정의 만들기

이제 확장 기능이 설치되었으므로 Azure DevOps 계정에 로그인하고 프로젝트 대시보드에서 프로젝트를 찾습니다. 프로젝트에 [빌드 파이프라인](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav)을 추가하거나 기존 빌드 파이프라인을 수정합니다. 이미 빌드 파이프라인이 있다면 [빌드 정의에 에뮬레이터 빌드 작업 추가](#addEmulatorBuildTaskToBuildDefinition)로 건너뛸 수 있습니다.

1. 새 빌드 정의를 만들려면 Azure DevOps에서 **빌드** 탭으로 이동합니다. **+새로 만들기**로 이동합니다.  \> **새 빌드 파이프라인**

   ![새 빌드 파이프라인 만들기](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png)

2. 원하는 **원본**, **팀 프로젝트**, **리포지토리**, **수동 및 예약된 빌드의 기본 분기**를 선택합니다. 필요한 옵션을 선택한 다음, **계속**을 선택합니다.

   ![팀 프로젝트, 리포지토리 및 빌드 파이프라인에 대한 분기 선택](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

3. 마지막으로, 빌드 파이프라인에 대해 원하는 템플릿을 선택합니다. 이 자습서에서는 **ASP.NET** 템플릿을 선택합니다. 

이제 빌드 파이프라인을 Azure Cosmos DB 에뮬레이터 빌드 작업을 사용하도록 설정할 수 있습니다. 

## <a name="addEmulatorBuildTaskToBuildDefinition"></a>빌드 파이프라인에 작업 추가

1. 빌드 파이프라인에 작업을 추가하기 전에 에이전트 작업을 추가해야 합니다. 빌드 파이프라인으로 이동하여 **...** 를 선택하고 **에이전트 작업 추가**를 선택합니다.

1. 다음으로 에이전트 작업 옆에 있는 **+** 기호를 선택하여 에뮬레이터 빌드 작업을 추가합니다. 검색 상자에서 **cosmos**를 검색하고 **Azure Cosmos DB 에뮬레이터**를 선택하여 에이전트 작업에 추가합니다. 빌드 작업은 Cosmos DB 에뮬레이터의 인스턴스를 이미 실행 중인 컨테이너를 시작합니다. Azure Cosmos DB 에뮬레이터 작업은 에뮬레이터가 실행 상태에 있어야 하는 다른 작업보다 먼저 배치해야 합니다.

   ![빌드 정의에 에뮬레이터 빌드 작업 추가](./media/tutorial-setup-ci-cd/addExtension_3.png)

이 자습서에서는 테스트가 실행되기 전에 에뮬레이터가 사용 가능한지 확인하기 위해 작업을 처음에 추가할 것입니다.

## <a name="configure-tests-to-use-the-emulator"></a>에뮬레이터를 사용하도록 테스트 구성

이제 에뮬레이터를 사용하도록 테스트를 구성합니다. 에뮬레이터 빌드 작업은 빌드 파이프라인의 모든 작업이 요청을 발급할 수 있는 'CosmosDbEmulator.Endpoint' 환경 변수를 내보냅니다. 

이 자습서에서는 [Visual Studio 테스트 작업](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md)을 사용하여 **.runsettings** 파일을 통해 구성된 단위 테스트를 실행합니다. 단위 테스트 설정에 대한 자세한 내용은 [설명서](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017)를 참조하세요. 이 문서에서 사용하는 Todo 애플리케이션 코드에 대한 완전한 샘플은 [Github](https://github.com/Azure-Samples/documentdb-dotnet-todo-app)에서 사용할 수 있습니다.

애플리케이션의 단위 테스트에 전달할 매개 변수를 정의하는 **.runsettings** 파일의 예제는 다음과 같습니다. 사용된 `authKey` 변수는 에뮬레이터에 대해 [잘 알려진 키](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests)입니다. 이 `authKey`는 에뮬레이터 빌드 작업에서 예상되는 키이며, **.runsettings** 파일에서 정의되어야 합니다.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```

Azure Cosmos DB의 MongoDB API를 사용하는 애플리케이션에 대한 CI/CD 파이프라인을 설정하는 경우 기본적으로 10255 포트 번호가 연결 문자열에 포함됩니다. 그러나 이 포트는 현재 열려 있지 않아 대안으로 포트 10250을 사용하여 연결을 설정합니다. 지원되는 포트 번호가 10255 대신 10250이라는 점을 제외하고는 Azure Cosmos DB의 MongoDB API 연결 문자열이 동일하게 유지됩니다.

이러한 매개 변수 `TestRunParameters`는 애플리케이션 테스트 프로젝트의 `TestContext` 속성을 통해 참조됩니다. Cosmos DB에 대해 실행되는 테스트의 예제는 다음과 같습니다.

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Visual Studio 테스트 작업의 실행 옵션으로 이동합니다. **설정 파일** 옵션에서 **.runsettings** 파일을 사용하여 테스트를 구성하도록 지정합니다. **테스트 실행 매개 변수 재정의** 옵션에서 `-endpoint $(CosmosDbEmulator.Endpoint)`에 추가합니다. 이렇게 하면 **.runsettings** 파일에 정의된 작업 대신 에뮬레이터 빌드 작업의 엔드포인트를 참조하도록 테스트 작업을 구성합니다.  

![에뮬레이터 빌드 작업 엔드포인트를 사용하여 엔드포인트 변수 재정의](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>빌드를 실행합니다.

이제 빌드를 **저장하고 큐에 대기**시킵니다. 

![빌드 저장 및 실행](./media/tutorial-setup-ci-cd/runBuild_1.png)

빌드가 시작되면 Cosmos DB 에뮬레이터 작업이 에뮬레이터가 설치된 Docker 이미지를 끌어오기 시작합니다. 

![빌드 저장 및 실행](./media/tutorial-setup-ci-cd/runBuild_4.png)

빌드가 완료된 후에 테스트가 통과되고 빌드 작업에서 Cosmos DB 에뮬레이터에 대해 모두 실행됩니다.

![빌드 저장 및 실행](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="next-steps"></a>다음 단계

로컬 개발 및 테스트에 에뮬레이터를 사용하는 방법을 자세히 알아보려면 [로컬 개발 및 테스트를 위해 Azure Cosmos DB 에뮬레이터 사용](https://docs.microsoft.com/azure/cosmos-db/local-emulator)을 참조하세요.

에뮬레이터 SSL 인증서를 내보내려면 [Java, Python 및 Node.js에서 사용할 Azure Cosmos DB 에뮬레이터 인증서 내보내기](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)를 참조하세요.
