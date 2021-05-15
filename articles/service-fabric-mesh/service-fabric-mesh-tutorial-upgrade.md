---
title: 자습서 - Azure Service Fabric Mesh 애플리케이션 업그레이드
description: 이 자습서는 시리즈의 4부이며, Visual Studio에서 직접 Azure Service Fabric Mesh 애플리케이션을 업그레이드하는 방법을 보여 줍니다.
author: georgewallace
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-csharp
ms.openlocfilehash: 1020613eb43177ba159601f253848f8d03f385a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625435"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>자습서: Visual Studio를 사용하여 Service Fabric 애플리케이션을 업그레이드하는 방법 알아보기

> [!IMPORTANT]
> Azure Service Fabric Mesh의 미리 보기가 사용 중지되었습니다. 새 배포는 더이상 Service Fabric Mesh API를 통해 허용되지 않습니다. 기존 배포에 대한 지원은 2021년 4월 28일까지 계속됩니다.
> 
> 자세한 내용은 [Azure Service Fabric Mesh 미리 보기 사용 중지](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)를 참조하세요.

이 자습서는 시리즈의 4부이며, Visual Studio에서 직접 Azure Service Fabric Mesh 애플리케이션을 업그레이드하는 방법을 보여 줍니다. 업그레이드에는 코드 업데이트와 구성 업데이트가 둘 다 포함됩니다. Visual Studio에서 업그레이드 및 게시 단계는 동일합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Visual Studio를 사용하여 Service Fabric Mesh 서비스 업그레이드

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * [Visual Studio에서 Service Fabric Mesh 앱 만들기](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [로컬 개발 클러스터에서 실행 중인 Service Fabric Mesh 앱 디버그](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Service Fabric Mesh 앱 배포](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Service Fabric Mesh 앱 업그레이드
> * [Service Fabric Mesh 리소스 정리](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하기 전에:

* 할 일 앱을 배포하지 않은 경우 [Service Fabric Mesh 웹 애플리케이션 게시](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)의 지침을 따르세요.

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Visual Studio를 사용하여 Service Fabric Mesh 서비스 업그레이드

이 문서에서는 애플리케이션 내에서 마이크로 서비스를 업그레이드하는 방법을 보여 줍니다. 이 예제에서는 `WebFrontEnd` 서비스를 수정하여 작업 범주를 표시하고 지정된 CPU의 양을 늘립니다. 그런 다음 배포된 서비스를 업그레이드합니다.

## <a name="modify-the-config"></a>구성 수정

Service Fabric Mesh 앱을 만들 때 Visual Studio는 각 배포 환경(클라우드 및 로컬)에 대한 **parameters.yaml** 파일을 추가합니다. 이러한 파일에서는 service.yaml 또는 network.yaml 등의 Mesh *.yaml 파일에서 참조할 수 있는 매개 변수 및 해당 값을 정의할 수 있습니다.  Visual Studio는 서비스에서 사용할 수 있는 CPU의 양과 같은 몇 가지 변수를 제공합니다.

**WebFrontEnd** 서비스가 더 많이 사용될 것으로 예측하여 `WebFrontEnd_cpu` 매개 변수를 업데이트하여 CPU 리소스를 `1.5`로 업데이트합니다.

1. **todolistapp** 프로젝트의 **환경** > **클라우드** 아래에서 **parameters.yaml** 파일을 엽니다. `WebFrontEnd_cpu`을 `1.5` 값으로 수정합니다. 매개 변수 이름 앞에는 서로 다른 서비스에 적용되는 동일한 이름의 매개 변수와 구별하기 위해 서비스 이름 `WebFrontEnd_`를 사용하는 것이 좋습니다.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. **WebFrontEnd** > **Service Resources** 에서 **WebFrontEnd** 프로젝트의 **service.yaml** 파일을 엽니다.

    `resources:` 섹션에서 `cpu:`가 `"[parameters('WebFrontEnd_cpu')]"`로 설정되어 있습니다. 프로젝트를 클라우드에 대해 빌드하는 경우 `'WebFrontEnd_cpu`에 대한 값은 **환경** > **클라우드** > **parameters.yaml** 파일에서 가져오며 `1.5`가 됩니다. 프로젝트가 로컬에서 실행되도록 빌드하는 경우 값은 **환경** > **로컬** > **parameters.yaml** 파일에서 가져오며 '0.5'가 됩니다.

> [!Tip]
> 기본적으로 profile.yaml 파일의 피어인 매개 변수 파일은 해당 profile.yaml 파일의 값을 제공하는 데 사용됩니다.
> 예를 들어, 환경 > 클라우드 > parameters.yaml은 환경 > 클라우드 > profile.yaml에 대한 매개 변수 값을 제공합니다.
>
> profile.yaml 파일에 `parametersFilePath=”relative or full path to the parameters file”`을 추가하여 이를 재정의할 수 있습니다. 예를 들어, `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` 또는 `parametersFilePath=”..\CommonParameters.yaml”`입니다.

## <a name="modify-the-model"></a>모델 수정

코드 변경을 도입하려면 `ToDoItem.cs` 파일의 `ToDoItem` 클래스에 `Category` 속성을 추가합니다.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

그런 다음, 동일한 파일에서 `Load()` 메서드를 업데이트하여 범주를 기본 문자열로 설정합니다.

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>서비스 수정

`WebFrontEnd` 프로젝트는 할 일 목록 항목을 표시하는 웹 페이지가 있는 ASP.NET Core 애플리케이션입니다. `WebFrontEnd` 프로젝트에서 `Index.cshtml`을 열고 아래 표시된 두 줄을 추가하여 작업 범주를 표시합니다.

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

앱을 빌드하고 실행하여 작업을 나열하는 새 범주 열이 웹 페이지에 표시되는지 확인합니다.

## <a name="upgrade-the-app-from-visual-studio"></a>Visual Studio에서 앱 업그레이드

코드 업그레이드 또는 구성 업그레이드(이 경우 업그레이드를 둘 다 수행)에 관계없이 Visual Studio에서 **todolistapp** 을 마우스 오른쪽 단추로 클릭하여 Azure의 Service Fabric Mesh 앱을 업그레이드한 후 **게시...** 를 선택합니다.

그러면 **Service Fabric 애플리케이션 게시** 대화 상자가 표시됩니다.

**대상 프로필** 드롭다운을 사용하여 이 배포에 사용할 profile.yaml 파일을 선택합니다. 클라우드에서 앱을 업그레이드하고 있으므로 **cloud.yaml** 를 선택하면 해당 파일에 정의된 `WebFrontEnd_cpu` 값 1.0이 사용됩니다.

![Visual Studio의 Service Fabric Mesh 게시 대화 상자](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Azure 계정과 구독을 선택합니다. 할 일 앱을 Azure에 처음 게시했을 때 사용한 위치로 **위치** 를 설정합니다. 이 문서에서는 **미국 동부** 를 사용했습니다.

할 일 앱을 Azure에 처음 게시했을 때 사용한 리소스 그룹으로 **리소스 그룹** 을 설정합니다.

할 일 앱을 Azure에 처음 게시했을 때 만든 Azure Container Registry 이름으로 **Azure Container Registry** 를 설정합니다.

게시 대화 상자에서 **게시** 단추를 눌러 Azure의 할 일 앱을 업그레이드합니다.

Visual Studio **출력** 창에서 **Service Fabric 도구** 창을 선택하여 업그레이드 진행률을 모니터링합니다. 

이미지가 작성되고 Azure Container Registry에 푸시된 후에는 Azure Portal에서 배포를 모니터링하기 위해 클릭할 수 있는 출력에 **상태** 링크가 표시됩니다.

업그레이드가 완료되면 **Service Fabric 도구** 출력에 애플리케이션의 IP 주소와 포트가 URL 형식으로 표시됩니다.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

웹 브라우저를 열고, URL로 이동하여 Azure에서 실행되는 웹 사이트를 확인합니다. 이제 범주 열이 포함된 웹 페이지가 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서 부분에서는 다음에 대해 알아보았습니다.
> [!div class="checklist"]
> * Visual Studio를 사용하여 Service Fabric Mesh 앱을 업그레이드하는 방법

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [Service Fabric Mesh 리소스 정리](service-fabric-mesh-tutorial-cleanup-resources.md)