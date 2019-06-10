---
title: .NET Core 및 Visual Studio를 사용하여 Azure Dev Spaces를 통한 팀 개발
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Azure에서 컨테이너 및 마이크로 서비스를 통한 신속한 Kubernetes 개발
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: c3a988a831ad1069e5988f9c67e92a85a7a44840
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65765197"
---
# <a name="team-development-with-azure-dev-spaces"></a>Azure Dev Spaces로 팀 개발

이 자습서에서는 개발자 팀이 Dev Spaces를 사용하여 동일한 Kubernetes 클러스터에서 동시에 공동 작업을 수행할 수 있는 방법을 알아봅니다.

## <a name="learn-about-team-development"></a>팀 개발 알아보기

지금까지 애플리케이션에서 작업하는 유일한 개발자인 것처럼 애플리케이션 코드를 실행했습니다. 이 섹션에서는 Azure Dev Spaces에서 팀 개발을 간소화하는 방법에 대해 알아봅니다.
* 필요에 따라 공유 개발 공간 또는 고유한 개발 공간에서 작업하여 개발자 팀이 동일한 환경에서 작업하도록 설정합니다.
* 각 개발자가 다른 개발자를 방해하지 않고 격리된 상태에서 자신의 코드를 반복할 수 있도록 지원합니다.
* 모의 개체를 만들거나 종속성을 시뮬레이션할 필요 없이 코드를 커밋하기 전에 엔드투엔드 코드를 테스트합니다.

### <a name="challenges-with-developing-microservices"></a>마이크로 서비스 개발 문제
현재 샘플 애플리케이션은 복잡하지 않습니다. 하지만 실제 개발 환경에서는 더 많은 서비스를 추가하고 개발 팀이 성장함에 따라 문제가 곧 발생합니다.

* 필요한 모든 서비스를 한 번에 실행하기에 개발 머신의 리소스가 충분치 않을 수도 있습니다.
* 일부 서비스는 공개적으로 연결할 수 있어야 합니다. 예를 들어 서비스에는 웹후크에 대응하는 엔드포인트가 필요할 수도 있습니다.
* 서비스의 서브넷을 실행하려는 경우 모든 서비스 간 전체 종속성 계층 구조를 알아야 합니다. 이 계층 구조를 결정하기란 어려울 수 있는데, 특히 서비스 수가 늘어날수록 어렵습니다.
* 일부 개발자는 서비스 종속성 대부분을 시뮬레이션하거나 모델링하는 데 의존합니다. 이 방법은 유용할 수 있지만 이러한 모의 개체 관리는 곧 개발 비용에 영향을 줄 수 있습니다. 또한 이 접근법은 개발 환경을 프로덕션과는 다르게 보이게 하는데, 이로 인해 감지하기 힘든 버그들이 발생할 수 있습니다.
* 따라서 모든 종류의 통합 테스트를 수행하는 것이 어려워집니다. 통합 테스트는 현실적으로 커밋 후에만 수행할 수 있습니다. 이는 나중에 개발 주기에서 문제가 발생한다는 것을 의미합니다.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>공유 개발 공간에서 작업
Azure Dev Spaces를 사용하면 Azure에서 *공유* 개발 공간을 설정할 수 있습니다. 각 개발자는 애플리케이션의 일부에만 집중할 수 있으며, 시나리오에 종속된 다른 모든 서비스와 클라우드 리소스가 이미 포함되어 있는 개발 공간에서 *사전 커밋 코드*를 반복적으로 개발할 수 있습니다. 종속성은 항상 최신이며, 개발자는 프로덕션을 미러하는 방식으로 작업합니다.

### <a name="work-in-your-own-space"></a>개발자 고유의 공간에서 작업
서비스에 대한 코드를 개발하고 이 코드를 체크 인할 준비가 되기 전에 코드 상태가 좋지 않은 경우가 많습니다. 아직도 코드를 반복적으로 만들고, 테스트하고, 솔루션에서 실험합니다. Azure Dev Spaces는 **공간**이라는 개념을 제공합니다. 이 공간을 사용하면 팀 구성원을 방해할 염려 없이 격리된 상태에서 작업할 수 있습니다.

## <a name="use-dev-spaces-for-team-development"></a>팀 개발에 Dev Spaces 사용
*webfrontend* -> *mywebapi* 샘플 애플리케이션을 사용하여 이러한 아이디어를 구체적인 예제로 설명해보겠습니다. 개발자 Scott이 *mywebapi* 서비스, *오직* 해당 서비스만 변경해야 하는 시나리오를 가정해보겠습니다. *webfrontend*는 Scott 업데이트의 일부로 변경할 필요가 없습니다.

Scott은 Dev Spaces를 사용하지 _않고도_ 몇 가지 방법으로 자신의 업데이트를 개발하고 테스트할 수는 있습니다. 그 중 어떤 것도 이상적인 방법은 아닙니다.
* 모든 구성 요소를 로컬로 실행합니다. 이를 위해서는 Docker가 설치된 더 강력한 개발 머신과 잠재적으로 MiniKube가 필요합니다.
* Kubernetes 클러스터에서 격리된 네임스페이스의 모든 구성 요소를 실행합니다. *webfrontend*는 변경되지 않으므로 격리된 네임스페이스를 사용하는 것은 클러스터 리소스를 낭비하는 방법입니다.
* *mywebapi*만 실행하고 테스트를 위해 수동 REST 호출을 만듭니다. 이 테스트 유형은 전체 엔드투엔드 흐름을 테스트하지 않습니다.
* 개발자가 다른 *mywebapi*의 인스턴스에 요청을 보낼 수 있는 *webfrontend*에 개발중점 코드를 추가합니다. 이 코드를 추가하면 *webfrontend* 서비스가 복잡해집니다.

### <a name="set-up-your-baseline"></a>기준 설정
먼저 서비스의 기준을 배포해야 합니다. 이 배포는 로컬 코드의 동작과 확인된 버전을 손쉽게 비교할 수 있도록 “마지막으로 성공한 항목”을 나타냅니다. 그런 다음, 더 큰 애플리케이션의 컨텍스트 내에서 *mywebapi*에 대한 변경 내용을 테스트할 수 있도록 이 기준에 따라 자식 공간을 만듭니다.

1. [Dev Spaces 샘플 애플리케이션](https://github.com/Azure/dev-spaces)을 복제합니다. `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. 원격 분기 *azds_updates*를 확인합니다. `git checkout -b azds_updates origin/azds_updates`
1. 두 서비스에 대한 F5/디버그 세션은 모두 닫고, 프로젝트는 Visual Studio 창에서 열어 둡니다.
1. _mywebapi_ 프로젝트가 있는 Visual Studio 창으로 전환합니다.
1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
1. 왼쪽의 **디버그** 탭을 선택하여 Azure Dev Spaces 설정을 표시합니다.
1. **변경**을 선택하여 F5 또는 Ctrl+F5를 누를 때 서비스에서 사용되는 공간을 만듭니다.
1. 공간 드롭다운에서 **\<새 공간 만들기...\>** 를 선택합니다.
1. 부모 공간이 **\<none\>** 으로 설정되어 있는지 확인하고 **dev**를 입력합니다. 확인을 클릭합니다.
1. Ctrl+F5를 눌러 연결된 디버거 없이 _mywebapi_를 실행합니다.
1. 마찬가지로, _webfrontend_ 프로젝트가 있는 Visual Studio 창으로 전환하고, Ctrl+F5를 눌러 실행합니다.

> [!Note]
> Ctrl+F5를 눌러 웹 페이지를 처음 표시한 후에 브라우저를 새로 고쳐야 하는 경우가 가끔 있습니다.

> [!TIP]
> 위의 단계는 기준을 수동으로 설정하지만, 팀에서 CI/CD를 사용하여 자동으로 최신 커밋된 코드로 기준을 유지하는 것이 좋습니다.
>
> 다음 다이어그램과 유사한 워크플로를 만들 수 있는 [Azure DevOps를 사용하여 CI/CD를 설정하기 위한 가이드](how-to/setup-cicd.md)를 확인해보세요.
>
> ![CI/CD 다이어그램의 예제](media/common/ci-cd-complex.png)

공용 URL을 열고 웹앱으로 이동하는 모든 사용자는 작성한 코드 경로를 호출하며, 이 코드는 기본 _dev_ 공간을 사용하여 두 서비스 모두를 통해 실행됩니다. 이제 *mywebapi*를 계속 개발한다고 가정할 때 개발 공간을 사용하는 다른 개발자를 방해하지 않고 수행하려면 어떻게 해야 할까요? 이렇게 하려면 자신만의 고유한 공간을 설정합니다.

### <a name="create-a-new-dev-space"></a>새 개발 환경 만들기
Visual Studio 내에서 서비스를 F5 또는 Ctrl+F5로 누르면 사용할 공간을 추가로 만들 수 있습니다. 원하는 공간을 호출할 수 있으며 그 의미(예: _sprint4_ 또는 _demo_)에 대해서도 자유롭게 사용할 수 있습니다.

새 공간을 만들려면 다음을 수행합니다.
1. *mywebapi* 프로젝트가 있는 Visual Studio 창으로 전환합니다.
2. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
3. 왼쪽의 **디버그** 탭을 선택하여 Azure Dev Spaces 설정을 표시합니다.
4. 여기서 F5 또는 Ctrl+F5를 누르면 사용할 클러스터 및/또는 공간을 변경하거나 만들 수 있습니다. *이전에 만든 Azure 개발 공간이 선택되어 있는지 확인합니다*.
5. 공간 드롭다운에서 **\<새 공간 만들기...\>** 를 선택합니다.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. **공간 추가** 대화 상자에서 부모 공간을 **dev**로 설정하고 새 공간의 이름을 입력합니다. 새 공간 이름에 사용자 이름(예: "scott")을 사용하면 해당 사용자가 작업 중인 공간이 동료에게 식별될 수 있습니다. **확인**을 클릭합니다.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. 이제 프로젝트 속성 페이지에서 선택한 AKS 클러스터와 새 공간이 표시됩니다.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>*mywebapi*에 대한 코드 업데이트

1. *mywebapi* 프로젝트에서 다음과 같이 코드를 `Controllers/ValuesController.cs` 파일의 `string Get(int id)` 메서드로 변경합니다.
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. 이 업데이트된 코드 블록에 중단점을 설정합니다(이미 이전에 설정한 중단점일 수 있음).
3. F5를 누르면 _mywebapi_ 서비스가 시작됩니다. 즉, 선택한 공간을 사용하여 클러스터에서 서비스가 시작됩니다. 이 사례의 선택한 공간은 _scott_입니다.

서로 다른 공간이 작동하는 방식을 이해하는 데 도움이 되는 다이어그램은 다음과 같습니다. 자주색 경로는 _dev_ 공간을 통한 요청을 나타내며, URL 앞에 공백이 없는 경우 사용되는 기본 경로입니다. 분홍색 경로는 _dev/scott_ 공간을 통한 요청을 나타냅니다.

![](media/common/Space-Routing.png)

기본 제공되는 Azure Dev Spaces 기능을 사용하면 각 개발자가 자신의 공간에서 서비스의 전체 스택을 다시 만들 필요 없이 공유 환경에서 코드를 엔드투엔드에 테스트할 수 있습니다. 이 라우팅에서는 이 가이드의 이전 단계에서 설명한 대로 앱 코드에서 전파 헤더를 전달해야 합니다.

### <a name="test-code-running-in-the-devscott-space"></a>_dev/scott_ 공간에서 실행 중인 코드 테스트
*webfrontend*와 함께 새 버전의 *mywebapi*를 테스트하려면 브라우저를 *webfrontend*에 대한 공용 액세스 지점 URL로 열고(예: http://dev.webfrontend.123456abcdef.eus.azds.io)) 정보 페이지로 이동합니다. "Hello from webfrontend and Hello from mywebapi.(webfrontend에서 보낸 Hello 및 mywebapi에서 보낸 Hello입니다.)"라는 원래 메시지가 표시됩니다.

이제 URL에 "scott.s" 부분을 추가하여 http://scott.s.dev.webfrontend.123456abcdef.eus.azds.io와 비슷한 내용을 읽고 브라우저를 새로 고칩니다. *mywebapi* 프로젝트에서 설정한 중단점에 적중되어야 합니다. F5 키를 클릭하여 계속 진행합니다. 그러면 브라우저에서 "Hello with webfrontend and mywebapi(webfrontend 및 mywebapi를 통한 Hello)"라는 새 메시지가 표시됩니다. 이는 *mywebapi*의 업데이트된 코드 경로가 _dev/scott_ 공간에서 실행되기 때문입니다.

항상 최신 변경 사항을 포함하는 _dev_ 공간을 확보하고 난 후, 이 자습서 섹션에서 설명한 대로 DevSpace의 공간 기반 라우팅을 이용하도록 애플리케이션이 설계되었다고 가정하면 Dev Spaces가 더 큰 애플리케이션의 컨텍스트 내에서 새로운 기능을 테스트하는 데 얼마나 유용한지를 쉽게 확인할 수 있을 것입니다. 프라이빗 공간에 _모든_ 서비스를 배포하는 대신, _dev_에서 파생되는 프라이빗 공간을 만들고 실제 작업하는 서비스만 "작동"시킬 수 있습니다. Dev Spaces 라우팅 인프라는 _dev_ 공간에서 실행 중인 최신 버전으로 기본값을 다시 설정하는 동안 프라이빗 공간 외에 찾을 수 있는 한 많은 서비스를 활용하여 나머지 부분을 처리합니다. 그리고 더 좋은 것은 _여러_ 개발자들이 서로를 방해하지 않으면서 자신의 공간에서 동시에 다양한 서비스를 적극적으로 개발할 수 있다는 점입니다.

### <a name="well-done"></a>모두 완료되었습니다!
시작 가이드를 완료했습니다! 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure에서 관리되는 Kubernetes 클러스터를 사용하여 Azure Dev Spaces를 설정합니다.
> * 컨테이너에서 반복적으로 코드를 개발합니다.
> * 독립적으로 별도의 두 서비스를 개발하고, Kubernetes의 DNS 서비스 검색을 사용하여 다른 서비스를 호출합니다.
> * 팀 환경에서 코드를 생산적으로 개발하고 테스트합니다.
> * Dev Spaces를 통해 기능의 기준을 설정하여 더 큰 마이크로 서비스 애플리케이션의 컨텍스트 내에서 격리된 변경 내용을 손쉽게 테스트합니다.

Azure Dev Spaces를 알아보았으므로 [팀 멤버와 개발 공간을 공유](how-to/share-dev-spaces.md)하고 함께 공동 작업을 수행하는 것이 얼마나 쉬운지 알 수 있습니다.

## <a name="clean-up"></a>정리
모든 개발 환경 및 그 안에서 실행되는 서비스를 포함하여 클러스터에서 Azure Dev Spaces 인스턴스를 완전히 삭제하려면 `az aks remove-dev-spaces` 명령을 사용합니다. 이 작업은 되돌릴 수 없습니다. 클러스터에서 Azure Dev Spaces에 대한 지원을 추가할 수 있지만 마치 다시 시작하는 것 같을 것입니다. 이전 서비스와 공간을 복원할 수 없습니다.

다음 예제에서는 활성 구독에 Azure Dev Spaces 컨트롤러를 나열한 다음, 리소스 그룹 'myaks-rg'에서 AKS 클러스터 'myaks'와 연결되는 Azure Dev Spaces 컨트롤러를 삭제합니다.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
