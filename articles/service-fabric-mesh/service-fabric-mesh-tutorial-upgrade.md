---
title: 자습서 - Azure Service Fabric Mesh 애플리케이션 업그레이드 | Microsoft Docs
description: Visual Studio를 사용하여 Service Fabric 애플리케이션을 업그레이드하는 방법 알아보기
services: service-fabric-mesh
documentationcenter: .net
author: tylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 7985c8e9e26126040d842ded998a953281daa2ae
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49953555"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>자습서: Visual Studio를 사용하여 Service Fabric 애플리케이션을 업그레이드하는 방법 알아보기

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

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에:

* 할 일 앱을 배포하지 않은 경우 [Service Fabric Mesh 웹 애플리케이션 게시](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)의 지침을 따르세요.

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Visual Studio를 사용하여 Service Fabric Mesh 서비스 업그레이드

이 문서에서는 애플리케이션 내에서 개별적으로 마이크로 서비스를 업그레이드하는 방법을 보여 줍니다.  이 예에서는 `WebFrontEnd` 서비스를 수정하여 작업 범주를 표시합니다. 그런 다음 배포된 서비스를 업그레이드합니다.

## <a name="modify-the-config"></a>구성 수정

업그레이드는 코드 변경, 구성 변경 또는 둘 다로 인해 발생할 수 있습니다.  구성 변경을 도입하려면 `WebFrontEnd` 프로젝트의 `service.yaml` 파일(**서비스 리소스** 노드 아래에 있음)을 엽니다.

웹 프런트 엔드가 많이 사용될 것으로 예상되므로 `resources:` 섹션에서 `cpu:`을 0.5에서 1.0으로 변경합니다. 이제 다음과 같이 표시됩니다.

```xml
              ...
              resources:
                requests:
                  cpu: 1.0
                  memoryInGB: 1
              ...
```

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

`WebFrontEnd` 프로젝트는 할 일 목록 항목을 표시하는 웹 페이지가 있는 ASP.NET Core 응용 프로그램입니다. `WebFrontEnd` 프로젝트에서 `Index.cshtml`을 열고 아래 표시된 두 줄을 추가하여 작업 범주를 표시합니다.

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

코드 업그레이드든 구성 업그레이드든(이 경우 업그레이드를 둘 다 수행) 관계없이 Azure의 Service Fabric Mesh 앱을 업그레이드하려면 Visual Studio에서 **todolistapp**을 마우스 오른쪽 단추로 클릭하고 **게시...** 를 선택합니다.

그러면 **Service Fabric 애플리케이션 게시** 대화 상자가 표시됩니다.

![Visual Studio의 Service Fabric Mesh 게시 대화 상자](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Azure 계정과 구독을 선택합니다. **위치**가 할 일 앱을 Azure에 처음 게시했을 때 사용한 위치로 설정되어 있는지 확인합니다. 이 문서에서는 **미국 동부**를 사용했습니다.

**리소스 그룹**이 할 일 앱을 Azure에 처음 게시했을 때 사용한 리소스 그룹으로 설정되어 있는지 확인합니다.

**Azure Container Registry**가 할 일 앱을 Azure에 처음 게시했을 때 만든 Azure Container Registry 이름으로 설정되어 있는지 확인합니다.

게시 대화 상자에서 **게시** 단추를 눌러 Azure의 할 일 앱을 업그레이드합니다.

Visual Studio **출력** 창에서 **Service Fabric 도구** 창을 선택하여 업그레이드 진행률을 모니터링할 수 있습니다. 업그레이드가 완료되면 **Service Fabric 도구** 출력에 애플리케이션의 IP 주소와 포트가 URL 형식으로 표시됩니다.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
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