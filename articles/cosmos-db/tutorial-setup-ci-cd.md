---
title: Azure Cosmos DB 에뮬레이터 빌드 작업을 사용하여 CI/CD 파이프라인 설정
description: Cosmos DB 에뮬레이터 빌드 작업을 사용하여 VSTS(Visual Studio Team Services)에서 빌드 및 릴리스 워크플로를 설정하는 방법에 대한 자습서
services: cosmos-db
keywords: Azure Cosmos DB 에뮬레이터
author: deborahc
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 8/28/2018
ms.author: dech
ms.openlocfilehash: 37bb43435c34f14145b3642aa12c5cb0f16d780c
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43783877"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-visual-studio-team-services"></a>Visual Studio Team Services에서 Azure Cosmos DB 에뮬레이터 빌드 작업을 사용하여 CI/CD 파이프라인 설정

Azure Cosmos DB 에뮬레이터는 개발 목적으로 Azure Cosmos DB 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. 이 에뮬레이터를 사용하면 Azure 구독을 구입하거나 비용을 발생시키지 않고도 로컬에서 응용 프로그램을 테스트할 수 있습니다. 

VSTS(Visual Studio Team Services)에 대한 Azure Cosmos DB 에뮬레이터 빌드 작업을 사용하면 CI 환경에서와 동일한 작업을 수행할 수 있습니다. 빌드 작업을 사용하면 빌드 및 릴리스 워크플로의 일부로 에뮬레이터에 대한 테스트를 실행할 수 있습니다. 이 작업은 에뮬레이터가 이미 실행 중인 Docker 컨테이너를 스핀업하고, 빌드 정의의 나머지 부분에서 사용할 수 있는 엔드포인트를 제공합니다. 별도 컨테이너에서 실행되는 필요한 만큼 많은 에뮬레이터의 인스턴스를 만들고 시작할 수 있습니다. 

이 문서에서는 Cosmos DB 에뮬레이터 빌드 작업을 사용하여 테스트를 실행하는 ASP.NET 응용 프로그램에 대해 VSTS에서 CI 파이프라인을 설정하는 방법을 설명합니다. 

## <a name="install-the-emulator-build-task"></a>에뮬레이터 빌드 작업 설치

빌드 작업을 사용하려면 먼저 VSTS 조직에 설치해야 합니다. [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview)에서 **Azure Cosmos DB 에뮬레이터** 확장을 찾고, **무료 다운로드**를 클릭합니다.

![VSTS Marketplace에서 Azure Cosmos DB 에뮬레이터 빌드 작업 찾기 및 설치](./media/tutorial-setup-ci-cd/addExtension_1.png)

다음으로, 확장을 설치할 조직을 선택합니다. 

> [!NOTE]
> VSTS 조직에 확장을 설치하려면 계정 소유자 또는 프로젝트 컬렉션 관리자여야 합니다. 사용 권한이 없지만 계정 멤버인 경우 대신 확장을 요청할 수 있습니다. [자세한 정보](https://docs.microsoft.com/vsts/marketplace/faq-extensions?view=vsts#install-request-assign-and-access-extensions) 

![확장을 설치할 VSTS 조직을 선택합니다.](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>빌드 정의 만들기

이제 확장이 설치되면 확장을 [빌드 정의](https://docs.microsoft.com/vsts/pipelines/get-started-designer?view=vsts&tabs=new-nav)에 추가해야 합니다. 기존 빌드 정의를 수정하거나 새로 만들 수 있습니다. 이미 기존 빌드 정의가 있다면 [빌드 정의에 에뮬레이터 빌드 작업 추가](#addEmulatorBuildTaskToBuildDefinition)로 건너뛸 수 있습니다.

새 빌드 정의를 만들려면 VSTS에서 **빌드 및 릴리스** 탭으로 이동합니다. **+새로 만들기**로 이동합니다.

![새 빌드 정의 만들기](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png) 빌드를 사용할 팀 프로젝트, 리포지토리 및 분기를 선택합니다. 

![빌드 정의에 대한 팀 프로젝트, 리포지토리 및 분기 선택 ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

마지막으로, 빌드 정의에 대해 원하는 템플릿을 선택합니다. 이 자습서에서는 **ASP.NET** 템플릿을 선택합니다. 

![원하는 빌드 정의 템플릿 선택 ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_3.png)

이제 빌드 정의를 아래처럼 보이는 Azure Cosmos DB 에뮬레이터 빌드 작업을 사용하도록 설정할 수 있습니다. 

![ASP.NET 빌드 정의 템플릿](./media/tutorial-setup-ci-cd/CreateNewBuildDef_4.png)

## <a name="addEmulatorBuildTaskToBuildDefinition"></a>빌드 정의에 작업 추가

에뮬레이터 빌드 작업을 추가하려면 검색 상자에서 **cosmos**를 검색하고, **추가**를 선택합니다. 빌드 작업은 Cosmos DB 에뮬레이터의 인스턴스를 이미 실행 중인 컨테이너를 시작합니다. 따라서 기타 작업이 에뮬레이터를 실행하기 전에 해당 작업을 배치해야 합니다.

![빌드 정의에 에뮬레이터 빌드 작업 추가](./media/tutorial-setup-ci-cd/addExtension_3.png) 이 자습서에서는 테스트를 실행하기 전에 에뮬레이터를 사용할 수 있도록 1단계의 시작 부분에 작업을 추가합니다.
이제 완료된 빌드 정의는 다음과 같습니다. 

![ASP.NET 빌드 정의 템플릿](./media/tutorial-setup-ci-cd/CreateNewBuildDef_5.png)

## <a name="configure-tests-to-use-the-emulator"></a>에뮬레이터를 사용하도록 테스트 구성
이제 에뮬레이터를 사용하도록 테스트를 구성합니다. 에뮬레이터 빌드 작업은 빌드 파이프라인의 모든 작업이 요청을 발급할 수 있는 'CosmosDbEmulator.Endpoint' 환경 변수를 내보냅니다. 

이 자습서에서는 [Visual Studio 테스트 작업](https://github.com/Microsoft/vsts-tasks/blob/master/Tasks/VsTestV2/README.md)을 사용하여 **.runsettings** 파일을 통해 구성된 단위 테스트를 실행합니다. 단위 테스트 설정에 대한 자세한 내용은 [설명서](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017)를 참조하세요.

응용 프로그램의 단위 테스트에 전달할 매개 변수를 정의하는 **.runsettings** 파일의 예제는 다음과 같습니다. 사용된 `authKey` 변수는 에뮬레이터에 대해 [잘 알려진 키](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests)입니다. 이 `authKey`는 에뮬레이터 빌드 작업에서 예상되는 키이며, **.runsettings** 파일에서 정의되어야 합니다.

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
이러한 매개 변수 `TestRunParameters`는 응용 프로그램 테스트 프로젝트의 `TestContext` 속성을 통해 참조됩니다. Cosmos DB에 대해 실행되는 테스트의 예제는 다음과 같습니다. 

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

Visual Studio 테스트 작업의 실행 옵션으로 이동합니다. **설정 파일** 옵션에서 **.runsettings** 파일을 사용하여 테스트를 구성하도록 지정합니다. **테스트 실행 매개 변수 재정의** 옵션에서 ` -endpoint $(CosmosDbEmulator.Endpoint)`에 추가합니다. 이렇게 하면 **.runsettings** 파일에 정의된 작업 대신 에뮬레이터 빌드 작업의 엔드포인트를 참조하도록 테스트 작업을 구성합니다.  

![에뮬레이터 빌드 작업 엔드포인트를 사용하여 엔드포인트 변수 재정의](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>빌드를 실행합니다.
이제 빌드를 저장하고 큐에 대기시킵니다. 

![빌드 저장 및 실행](./media/tutorial-setup-ci-cd/runBuild_1.png)

빌드가 시작되면 Cosmos DB 에뮬레이터 작업이 에뮬레이터가 설치된 Docker 이미지를 끌어오기 시작합니다. 

![빌드 저장 및 실행](./media/tutorial-setup-ci-cd/runBuild_4.png)

빌드가 완료된 후에 테스트가 통과되고 빌드 작업에서 Cosmos DB 에뮬레이터에 대해 모두 실행됩니다.

![빌드 저장 및 실행](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="next-steps"></a>다음 단계

로컬 개발 및 테스트에 에뮬레이터를 사용하는 방법을 자세히 알아보려면 [로컬 개발 및 테스트를 위해 Azure Cosmos DB 에뮬레이터 사용](https://docs.microsoft.com/azure/cosmos-db/local-emulator)을 참조하세요.

에뮬레이터 SSL 인증서를 내보내려면 [Java, Python 및 Node.js에서 사용할 Azure Cosmos DB 에뮬레이터 인증서 내보내기](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)를 참조하세요.
