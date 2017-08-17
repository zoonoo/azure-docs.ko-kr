---
title: "Service Fabric에 대한 .NET 응용 프로그램 만들기 | Microsoft Docs"
description: "ASP.NET Core 프런트 엔드 및 신뢰할 수 있는 서비스 상태 저장 백 엔드로 응용 프로그램을 만들고 클러스터에 배포하는 방법에 대해 알아봅니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: ryanwi, mikhegn
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 71d910bc0e459528805521ba991e5291396a3b8d
ms.contentlocale: ko-kr
ms.lasthandoff: 08/10/2017

---

# <a name="create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>ASP.NET Core Web API 프런트 엔드 서비스 및 상태 저장 백 엔드 서비스로 응용 프로그램을 만들고 배포
이 자습서는 시리즈의 1부로, ASP.NET Core Web API 프런트 엔드 및 상태 저장 백 엔드 서비스로 Azure Service Fabric 응용 프로그램을 만들어 데이터를 저장하는 방법을 보여 줍니다. 

![응용 프로그램 다이어그램](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

시리즈 1부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * ASP.NET Core Web API 서비스를 신뢰할 수 있는 서비스로 만들기
> * 상태 저장 신뢰할 수 있는 서비스 만들기
> * 서비스 원격 구현 및 서비스 프록시 사용

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * .NET Service Fabric 응용 프로그램 빌드
> * [응용 프로그램을 원격 클러스터에 배포](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Visual Studio Team Services를 사용하여 CI/CD 구성](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에:
- Azure 구독이 없는 경우 [평가판 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- [Visual Studio 2017을 설치](https://www.visualstudio.com/)하고 **Azure 개발**과 **ASP.NET 및 웹 개발** 워크로드를 설치합니다.
- [Service Fabric SDK를 설치](service-fabric-get-started.md)합니다.

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>ASP.NET Web API 서비스를 신뢰할 수 있는 서비스로 만들기
ASP.NET Core는 최신 웹 UI 및 Web API를 만드는 데 사용할 수 있는 가벼운 크로스 플랫폼 웹 개발 프레임워크입니다. ASP.NET Core가 Service Fabric과 통합되는 방식을 완전히 이해하려면 [Service Fabric Reliable Services의 ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) 문서를 읽어보는 것이 좋습니다. 현재는 이 가이드를 따라 작업하면 빠르게 시작할 수 있습니다. ASP.NET Core에 대한 자세한 내용은 [ASP.NET Core 설명서](https://docs.microsoft.com/aspnet/core/)를 참조하세요.

> [!NOTE]
> 이 자습서는 [Visual Studio 2017용 ASP.NET Core 도구](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/start-mvc)를 기준으로 합니다. Visual Studio 2015용 .NET Core 도구는 더 이상 업데이트되지 않습니다.

1. **관리자** 권한으로 Visual Studio를 시작합니다.

2. **파일**->**새로 만들기**->**프로젝트**로 프로젝트를 만듭니다.

3. **새 프로젝트** 대화 상자에서 **클라우드 > Service Fabric 응용 프로그램**을 선택합니다.

4. 응용 프로그램의 이름을 **MyApplication**으로 지정하고 **확인**을 누릅니다.

   ![Visual Studio의 새 프로젝트 대화 상자](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. **새 Service Fabric 서비스** 페이지에서 **상태 비저장 ASP.NET Core**를 선택하고 서비스 이름을 **MyWebAPIFrontEnd**로 지정합니다.
   
   ![새 서비스 대화 상자에서 ASP.NET 웹 서비스 선택](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. 다음 페이지에서는 ASP.NET Core 프로젝트 템플릿 집합을 제공합니다. 이 자습서에서는 **Web API**를 선택합니다. 그러나 전체 웹 응용 프로그램을 구축하는 동일한 개념을 적용할 수 있습니다.
   
   ![ASP.NET 프로젝트 유형 선택](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio는 응용 프로그램 및 서비스 프로젝트를 만들고 솔루션 탐색기에 표시합니다.

   ![ASP.NET core Web API 서비스를 사용하여 응용 프로그램 만들기를 수행하는 솔루션 탐색기]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="deploy-and-debug-the-application-locally"></a>로컬로 응용 프로그램 배포 및 디버그
이제 계속해서 응용 프로그램 디버그하고 ASP.NET Core Web API 템플릿이 제공하는 기본 동작으로 살펴보겠습니다.

Visual Studio에서 `F5`를 눌러 응용 프로그램을 디버깅하기 위해 배포합니다. **관리자** 권한으로 Visual Studio를 이전에 열지 않은 경우 `F5` 키가 실패합니다.

> [!NOTE]
> 로컬에서 처음으로 응용 프로그램을 실행하고 배포할 때 Visual Studio는 디버깅을 위해 로컬 클러스터를 만드는 데 다소 시간이 소요될 수 있습니다. Visual Studio 출력 창에 클러스터 생성 상태가 표시됩니다.

클러스터가 준비되면 시스템 트레이에서 Service Fabric 로컬 클러스터 관리자 응용 프로그램으로부터 알림을 받습니다.

1. 응용 프로그램을 디버그하려면 Visual Studio에서 F5 키를 누릅니다.
2. 배포가 완료되면 Visual Studio에서 ASP.NET Web API 서비스의 루트에 브라우저를 실행합니다. ASP.NET Core Web API 템플릿은 루트에 대한 기본 동작을 제공하지 않으므로 브라우저에서 오류가 발생합니다.
3. 브라우저에서 URL에 `/api/values`를 추가합니다. 이 요청은 Web API 템플릿에서 ValuesController의 `Get` 메서드를 호출합니다. 두 개의 문자열을 포함하는 JSON 배열인 템플릿에서 제공하는 기본 응답을 반환합니다.
   
![ASP.NET Core Web API 템플릿에서 반환되는 기본값](./media/service-fabric-tutorial-create-dotnet-app/browser-aspnet-template-values.png)

> [!NOTE]
> 응용 프로그램을 디버깅할 때 Visual Studio 2017의 기본 동작을 변경하려면 Service Fabric 응용 프로그램 프로젝트 **MyApplication**의 속성을 변경하면 됩니다.
> 이 자습서에서는 응용 프로그램 디버그 모드를 **응용 프로그램 새로 고침**으로 설정하고 응용 프로그램 URL 속성에 **'/api/values'**를 추가하여 디버깅 환경을 최적화합니다.
> 

응용 프로그램 디버깅을 중지하려면 Visual Studio로 돌아가 **Shift+F5** 키를 누릅니다.

### <a name="understanding-the-service-fabric-application-and-service"></a>Service Fabric 응용 프로그램 및 서비스 이해
이제 Visual Studio 솔루션에는 2개의 프로젝트가 있습니다.
1. Service Fabric 응용 프로그램 프로젝트 - **MyApplication**
    - 이 프로젝트는 코드를 직접 포함하지 않습니다. 대신 서비스 프로젝트의 집합을 참조합니다. 또한 응용 프로그램이 구성 및 배포되는 방식을 지정하는 다른 유형의 콘텐츠를 포함합니다.
2. 서비스 프로젝트 - **MyWebAPIFrontEnd**
    - 이 프로젝트는 서비스에 대한 코드 및 구성을 포함하는 ASP.NET Core Web API 프로젝트입니다. Controllers 폴더에서 ValuesController.cs 코드 파일을 보면 일반적인 ASP.NET Core Web API 컨트롤러임을 알 수 있습니다. Service Fabric에서 ASP.NET Core Web API를 신뢰할 수 있는 서비스로 실행할 때 컨트롤러의 일부로 작성하는 코드에 대해서는 특별한 요구 사항이 없습니다.

Service Fabric에서 응용 프로그램 모델에 대한 자세한 내용은 [Service Fabric에서 응용 프로그램 모델링](service-fabric-application-model.md)을 참조하세요.

서비스 프로젝트의 내용에 대한 자세한 내용은 [Reliable Services 시작](service-fabric-reliable-services-quick-start.md)을 참조하세요.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>상태 저장 백 엔드 서비스를 응용 프로그램에 추가
이제 응용 프로그램에서 실행되는 ASP.NET Web API 서비스가 있으므로 계속해서 상태 저장 신뢰할 수 있는 서비스 추가하여 응용 프로그램에 일부 데이터를 저장해 보겠습니다.

Service Fabric을 통해 신뢰할 수 있는 컬렉션을 사용하여 일관되고 안정적으로 서비스 내에 데이터를 바로 저장할 수 있습니다. 신뢰할 수 있는 컬렉션은 C# 컬렉션을 사용해본 적이 있는 사람에게 친숙한 신뢰할 수 있는 고가용성 컬렉션 클래스의 간단한 집합입니다.

이 자습서에서는 신뢰할 수 있는 컬렉션에 카운터 값을 저장하는 서비스를 만듭니다.

1. 솔루션 탐색기에서 응용 프로그램 프로젝트 내의 **서비스**를 마우스 오른쪽 단추로 클릭하고 **추가 > 새 Service Fabric 서비스**를 선택합니다.
   
    ![기존 응용 프로그램에 새 서비스 추가](./media/service-fabric-tutorial-create-dotnet-app/vs-add-new-service.png)

2. **새 Service Fabric 서비스** 대화 상자에서 **상태 저장 서비스**를 선택하고 서비스 이름을 **MyStatefulService**로 지정하고 **확인**을 누릅니다.

    ![Visual Studio의 새 서비스 대화 상자](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    서비스 프로젝트를 만들었으면 응용 프로그램에 두 개의 서비스가 있을 것입니다. 계속해서 응용 프로그램을 빌드하는 동안 같은 방법으로 더 많은 서비스를 추가할 수 있습니다. 각 서비스를 독립적으로 버전 지정 및 업그레이드할 수 있습니다.

### <a name="deploy-and-debug-the-application-locally"></a>로컬로 응용 프로그램 배포 및 디버그
응용 프로그램에 새 서비스가 추가되면 전체 응용 프로그램을 디버그하고 새 서비스의 기본 동작을 살펴보겠습니다.

응용 프로그램을 디버그하려면 Visual Studio에서 F5 키를 누릅니다.

> [!NOTE]
> 응용 프로그램 디버그 모드로 **응용 프로그램 새로 고침**을 사용하도록 선택한 경우 응용 프로그램의 빌드 출력 폴더에 대해 로컬 Service Fabric 클러스터 액세스 권한을 부여할지 묻는 메시지가 표시됩니다.
> 

응용 프로그램의 두 서비스 모두 로컬 Service Fabric 클러스터에 빌드, 배포 및 명시됩니다. 서비스가 시작되면 Visual Studio는 브라우저를 계속 시작하지만 **진단 이벤트 뷰어**를 자동으로 표시하며 서비스에서 추적 출력을 확인할 수 있습니다.
   
![진단 이벤트 뷰어](./media/service-fabric-tutorial-create-dotnet-app/diagnostic-events-viewer.png)

진단 이벤트 뷰어는 디버그되는 Visual Studio 솔루션에 포함되는 모든 서비스로부터 추적 메시지를 보여 줍니다. 진단 이벤트 뷰어를 일시 중지하여 서비스 메시지 중 하나를 확장하고 속성을 검사할 수 있습니다. 이렇게 하면 클러스터에서 어떤 서비스가 메시지를 내보냈고 서비스 인스턴스가 현재 실행 중인 노드가 무엇인지 그리고 기타 정보도 확인할 수 있습니다.

![진단 이벤트 뷰어](./media/service-fabric-tutorial-create-dotnet-app/expanded-diagnostics-viewer.png)

**serviceTypeName**이 **MyStatefulServiceType**이므로 서비스 메시지가 생성한 상태 저장 서비스에서 가져온 것임을 알 수 있습니다. “Current counter is...”라는 텍스트가 있는 메시지를 보내고 있는 것도 알 수 있습니다. 이 메시지는 **MyStatefulService.cs**의 `RunAsync` 메서드에서 강조 표시된 코드 줄에 의해 내보내지며 다음 스크린샷에 나와 있습니다.

![서비스 메시지 코드](./media/service-fabric-tutorial-create-dotnet-app/service-message-code.png)

서비스 및 응용 프로그램에서 진단 정보를 내보내는 방법은 [Azure Service Fabric 모니터링 및 진단](service-fabric-diagnostics-overview.md)을 참조하세요.

응용 프로그램 디버깅을 중지하려면 Visual Studio로 돌아가 **Shift+F5** 키를 누릅니다.

### <a name="understanding-the-mystatefulservice-code"></a>MyStatefulService 코드 이해
클러스터에 데이터를 저장하는 데 신뢰할 수 있는 사전을 어떻게 사용하는지 이해하기 위해 잠시 시간을 내어 **MyStatefulService** 서비스 코드를 살펴보겠습니다.

신뢰할 수 있는 사전에서 만들기, 업데이트 및 읽기와 관련된 서비스의 코드 5줄은 다음과 같습니다.

![신뢰할 수 있는 사전 코드](./media/service-fabric-tutorial-create-dotnet-app/reliable-dictionary-code.png)

1. 서비스의 `RunAsync` 메서드가 실행될 때마다(서비스가 시작될 때 발생) 이 코드 줄은 `myDictionary` 이름의 신뢰할 수 있는 사전을 서비스로 가져오거나 서비스에 추가합니다.
2. 신뢰할 수 있는 사전에 있는 값과의 모든 상호 작용에는 트랜잭션이 필요하며 이 코드 줄에 입력된 using 문으로 트랜잭션이 만들어집니다.
3. 이 코드 줄은 메서드 호출에 지정된 키와 연결된 값을 가져옵니다(예: `Counter`).
4. 이 코드 줄은 값을 증가시켜 키 `Counter`와 연결된 값을 업데이트합니다.
5. 이 메서드 호출은 트랜잭션을 커밋하고 업데이트된 값이 클러스터의 노드 쿼럼 간에 저장되면 반환합니다.

신뢰할 수 있는 사전 및 신뢰할 수 있는 컬렉션에 대한 자세한 내용은 [Azure Service Fabric 상태 저장 서비스의 신뢰할 수 있는 컬렉션 소개](service-fabric-reliable-services-reliable-collections.md)를 참조하세요.

## <a name="connect-the-services"></a>서비스 연결
다음이 단계에서는 두 서비스를 연결하고 프런트 엔드 Web API가 백 엔드 서비스 신뢰할 수 있는 사전에서 현재 값을 반환하도록 합니다.

서비스 패브릭은 신뢰할 수 있는 서비스와 유연하게 통신할 수 있는 방법을 제공합니다. 단일 응용 프로그램 내에는 TCP를 통해 액세스할 수 있는 서비스가 있을 수 있습니다. HTTP REST API 및 다른 서비스를 통해 액세스할 수 있는 기타 서비스는 웹 소켓을 통해 액세스할 수 있습니다. 제공되는 옵션 및 관련 장단점에 대한 배경 정보는 [서비스와의 통신](service-fabric-connect-and-communicate-with-services.md)을 참조하세요.

이 자습서에서는 [신뢰할 수 있는 서비스로 서비스 원격 호출](service-fabric-reliable-services-communication-remoting.md)을 사용합니다.

서비스 원격 접근 방식(원격 프로시저 호출 또는 RPC에서 모델링)에서 인터페이스를 정의하여 서비스에 대한 공용 계약의 역할을 합니다. 해당 인터페이스를 사용하여 그런 다음 서비스와 상호 작용하기 위한 프록시 클래스를 생성합니다.

### <a name="create-the-remoting-interface"></a>원격 인터페이스 만들기
두 서비스 간의 계약으로 작동하는 인터페이스를 만드는 것으로 시작합니다. 인터페이스는 인터페이스를 사용하는 모든 서비스에서 참조되어야 하므로 별도의 클래스 라이브러리 프로젝트에 인터페이스를 만듭니다.

1. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 프로젝트**를 선택합니다.
2. 왼쪽의 탐색 창에서 **Visual C#** 항목을 선택한 다음 **클래스 라이브러리(.NET Framework)** 템플릿을 선택합니다. .NET Framework 버전이 **4.5.2** 이상으로 설정되어 있는지 확인합니다.
   
    ![상태 저장 서비스에 대한 인터페이스 프로젝트 만들기](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-project.png)

3. 클래스 라이브러리 이름을 **MyStatefulService.Interface**로 지정하고 **확인**을 클릭합니다.

4. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭한 후 **솔루션용 NuGet 패키지 관리**를 클릭합니다.

5. **찾아보기**를 선택하고 **Microsoft.ServiceFabric.Services.Remoting**을 검색합니다. 솔루션의 세 가지 서비스 프로젝트에 대해 설치하도록 선택합니다. 
   
    ![서비스 NuGet 패키지 추가](./media/service-fabric-tutorial-create-dotnet-app/add-nuget.png)

6. 클래스 라이브러리에서 단일 메서드 `GetCountAsync`를 사용하여 인터페이스를 만들고, `Microsoft.ServiceFabric.Services.Remoting.IService`에서 해당 인터페이스를 확장합니다. 원격 인터페이스는 서비스 원격 인터페이스임을 나타내기 위해 이 인터페이스에서 파생되어야 합니다. 
   
    ```c#
    using Microsoft.ServiceFabric.Services.Remoting;  
    using System.Threading.Tasks;
    ...
    namespace MyStatefulService.Interface
    {           
        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```
7. 솔루션 탐색기에서 **MyStatefulService.Interface** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. **빌드** 탭을 선택한 후 **플랫폼 대상** 드롭다운에서 **x64** 값을 선택합니다. 

8. 변경 내용을 모두 저장합니다.

### <a name="implement-the-interface-in-your-stateful-service"></a>상태 저장 서비스에서 인터페이스 구현
인터페이스를 정의했으니 이제는 상태 저장 서비스를 구현해야 합니다.

1. **MyStatefulService** 프로젝트의 인터페이스가 포함된 클래스 라이브러리 프로젝트에 참조를 추가합니다.
   
    ![상태 저장 서비스의 클래스 라이브러리 프로젝트에 참조 추가](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-reference.png)

    ![상태 저장 서비스의 클래스 라이브러리 프로젝트에 참조 추가](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-reference-2.png)

2. `MyStatefulService.cs` 파일을 열고 확장하여 생성한 `ICounter` 인터페이스를 구현합니다.
   
    ```c#
    using MyStatefulService.Interface;
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. 이제 `ICounter` 인터페이스인 `GetCountAsync`에 정의된 단일 메서드를 구현합니다.
   
    ```c#
    public async Task<long> GetCountAsync()
    {
        var myDictionary =
          await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
   
        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```

    - 이 메서드는 `myDictionary`라는 신뢰할 수 있는 사전에 저장된 `Counter` 키 값을 반환합니다. 

### <a name="expose-the-stateful-service-using-service-remoting"></a>서비스 원격을 사용하여 상태 저장 서비스 공개
구현된 `ICounter` 인터페이스와 함께 마지막 단계는 서비스 원격 끝점을 여는 것입니다. 상태 저장 서비스의 경우 서비스 패브릭은 `CreateServiceReplicaListeners`라는 재정의 가능한 메서드를 제공합니다. 이 메서드로 서비스에 사용하려는 통신 형식에 따라 하나 이상의 통신 수신기를 지정할 수 있습니다.

이 경우에서는 기존 `CreateServiceReplicaListeners` 메서드를 바꾸고 `ServiceRemotingListener`의 인스턴스를 제공하며 이는 `ServiceProxy`을 통해 클라이언트에서 호출 가능한 RPC 끝점을 만듭니다.  

**MyStatefulService.cs** 파일에서 **CreateServiceReplicaListeners** 메서드를 변경하고 `Microsoft.ServiceFabric.Services.Remoting.Runtime` 네임스페이스에 using 문을 추가합니다.

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```

상태 저장 서비스는 서비스 원격을 사용하여 RPC를 통한 다른 서비스에서 트래픽을 받을 준비가 되었습니다.

### <a name="call-the-stateful-back-end-service-from-the-front-end-service"></a>프런트 엔드 서비스에서 상태 저장 백 엔드 서비스 호출
이제 백 엔드 서비스가 인터페이스를 공개했고 ASP.NET Web API 서비스에서 통신할 코드를 추가하는 작업만 하면 됩니다. 서비스 원격을 사용하여 통신하기 위해 값 컨트롤러에서 서비스 프록시를 사용 중입니다.

1. 솔루션 탐색기에서 **MyWebAPIFrontEnd**를 확장하고 **종속성**을 마우스 오른쪽 단추로 클릭하고 **참조 추가**를 선택합니다.  **MyStatefulService.Interface**를 선택하고 **확인**을 클릭합니다.
   
2. **Controllers** 폴더에서 `ValuesController.cs` 파일을 엽니다. 다음 using 문을 파일에 추가합니다.

    ```c#
    using MyStatefulService.Interface;
    using Microsoft.ServiceFabric.Services.Client;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
    ```
    
3. `Get` 메서드가 현재는 하드 코드된 문자열 배열 “value1” 및 “value2”만 반환하며 이 문자열은 앞서 브라우저에서 본 것과 일치합니다. 이것을 다음 코드로 바꿉니다.
   
    ```
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));
   
        long count = await counter.GetCountAsync();
   
        return new string[] { count.ToString() };
    }
    ```
 
    이 메서드의 첫 번째 코드 줄은 ICounter 인터페이스를 사용하여 상태 저장 서비스에 대해 ServiceProxy 개체를 만듭니다. ServiceProxy를 만들 때는 두 가지 정보, 즉 파티션 ID와 서비스 이름을 제공해야 합니다.
   
    상태 저장 서비스는 고객 ID 또는 우편 번호와 같은 키에 따라 데이터를 다른 버킷으로 분할하여 규모에 맞게 분할할 수 있습니다. 간단한 응용 프로그램에서 상태 저장 서비스에는 파티션이 하나밖에 없으므로 키는 문제가 되지 않습니다. 아무 키를 입력해도 같은 파티션으로 연결됩니다. 서비스 분할에 대해 자세히 알아보려면 [서비스 패브릭 Reliable Services 분할 방법](service-fabric-concepts-partitioning.md)을 참조하세요.
   
    서비스 이름은 양식 패브릭의 URI입니다(예: /&lt;application_name&gt;/&lt;service_name&gt;).
   
    서비스 패브릭에서는 이러한 두 가지 정보를 사용하여 요청을 보내야 하는 컴퓨터를 식별할 수 있습니다. 또한 `ServiceProxy` 클래스는 상태 저장 서비스 파티션을 호스트하는 컴퓨터에 장애가 발생하여 다른 컴퓨터가 그 역할을 대신해야 하는 경우를 원활하게 처리할 수 있습니다. 이 추상화를 사용하면 다른 서비스를 처리하기 위한 클라이언트 코드 쓰기 작업이 더 간단해집니다.
   
    프록시가 있으면 `GetCountAsync` 메서드를 호출하면 그 결과가 반환됩니다.

4. 수정된 응용 프로그램을 실행하려면 F5 키를 다시 누르면 됩니다. 이전과 마찬가지로, Visual Studio에서 자동으로 웹 프로젝트의 루트에 브라우저를 실행합니다. "api/values" 경로를 추가하면 반환되는 현재 카운터 값이 표시되야 합니다.
   
    ![브라우저에 표시되는 상태 저장 카운터 값](./media/service-fabric-tutorial-create-dotnet-app/browser-aspnet-counter-value.png)
   
    브라우저를 주기적으로 새로 고쳐 카운터 값 업데이트를 확인하세요.

응용 프로그램 디버깅을 중지하려면 Visual Studio로 돌아가 **Shift+F5** 키를 누릅니다.

## <a name="next-steps"></a>다음 단계
자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * ASP.NET Core Web API 서비스를 신뢰할 수 있는 서비스로 만들기
> * 상태 저장 신뢰할 수 있는 서비스 만들기
> * 서비스 원격 구현 및 서비스 프록시 사용

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [Azure에 응용 프로그램 배포](service-fabric-tutorial-deploy-app-to-party-cluster.md)
