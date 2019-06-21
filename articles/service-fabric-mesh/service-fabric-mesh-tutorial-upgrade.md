---
title: 자습서 - Azure Service Fabric Mesh 애플리케이션 업그레이드 | Microsoft Docs
description: Visual Studio를 사용하여 Service Fabric 애플리케이션을 업그레이드하는 방법 알아보기
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 23809abd06d626eb87e5d5d15d265f1769b97b66
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60809096"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>자습서: Visual Studio를 사용하여 Service Fabric 애플리케이션을 업그레이드하는 방법 알아보기

이 자습서는 시리즈의 4부이며, Visual Studio에서 직접 Azure Service Fabric Mesh 애플리케이션을 업그레이드하는 방법을 보여 줍니다. 업그레이드에는 코드 업데이트와 구성 업데이트가 둘 다 포함됩니다. Visual Studio 내에서 게시 및 업그레이드에 대 한 단계는 동일는 볼 수 있습니다.

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

이 아티클에서 응용 프로그램 내에서 마이크로 서비스를 업그레이드 하는 방법에 설명 합니다. 이 예제에서는 수정 된 `WebFrontEnd` 작업 범주를 표시 하 여 지정 되 고 CPU 양을 증가 시켜 서비스입니다. 그런 다음 배포된 서비스를 업그레이드합니다.

## <a name="modify-the-config"></a>구성 수정

Service Fabric 망상 조직 응용 프로그램을 만든 경우 Visual studio 추가 **parameters.yaml** 각 배포 환경 (클라우드 및 로컬) 파일입니다. 이러한 파일의 매개 변수와 service.yaml 등 network.yaml 메시 *.yaml 파일에서 참조 될 수 있는 해당 값을 정의할 수 있습니다.  Visual Studio 사용자에 대 한 서비스를 사용할 수는 얼마나 많은 CPU와 같은 일부 변수를 제공 합니다.

업데이트를 `WebFrontEnd_cpu` cpu 리소스를 업데이트 하려면 매개 변수 `1.5` 대비 하 여에서는 **WebFrontEnd** 서비스는 더 많이 사용 됩니다.

1. 에 **todolistapp** 프로젝트 아래에 있는 **환경** > **클라우드**엽니다는 **parameters.yaml** 파일. 수정 된 `WebFrontEnd_cpu`에 값을 `1.5`입니다. 매개 변수 이름을 서비스 이름 앞 `WebFrontEnd_` 이름이 같은 다른 서비스에 적용 되는 매개 변수에서 구분 하기 위해이 가장 좋습니다.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. 엽니다는 **WebFrontEnd** 프로젝트의 **service.yaml** 파일 **WebFrontEnd** > **Service 리소스**합니다.

    `resources:` 섹션인 `cpu:` 로 설정 된 `"[parameters('WebFrontEnd_cpu')]"`합니다. 클라우드에 대 한 값에 대 한 프로젝트를 빌드할 경우 `'WebFrontEnd_cpu` 에서 수행 될 합니다 **환경을** > **클라우드** > **parameters.yaml** 파일과 됩니다 `1.5`합니다. 값을 가져옵니다는 로컬로 실행 하는 프로젝트가 빌드되고, 하는 경우는 **환경을** > **로컬** > **parameters.yaml** 파일 및 '0.5' 됩니다.

> [!Tip]
> 기본적으로 profile.yaml 파일의 피어 인 매개 변수 파일을 사용할 profile.yaml 파일에 대 한 값을 제공 합니다.
> 예를 들어, 환경 > 클라우드 > 환경에 대 한 매개 변수 값을 제공 하는 parameters.yaml > 클라우드 > profile.yaml 합니다.
>
> Profile.yaml 파일에 다음을 추가 하 여이 재정의할 수 있습니다.`parametersFilePath=”relative or full path to the parameters file”` 예를 들어  또는 `parametersFilePath=”..\CommonParameters.yaml”`와 같습니다.

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

코드 업그레이드를 하거나 (여기서 보여드린 둘 다) 구성 업그레이드를 변경 하려는 여부, 마우스 오른쪽 단추로 클릭 하 여 Azure에서 Service Fabric 망상 조직 응용 프로그램 업그레이드 **todolistapp** 에서 Visual Studio를 선택 합니다 **게시 하는 중...**

그러면 **Service Fabric 애플리케이션 게시** 대화 상자가 표시됩니다.

사용 합니다 **대상 프로필** 드롭다운이이 배포에 사용할 profile.yaml 파일을 선택 합니다. 선택 클라우드에서 앱을 업그레이드 하 고는 **cloud.yaml** 드롭다운 목록에서를 사용 하는 `WebFrontEnd_cpu` 1.0 해당 파일에 정의 된 값입니다.

![Visual Studio의 Service Fabric Mesh 게시 대화 상자](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Azure 계정과 구독을 선택합니다. 설정 된 **위치** 원래 Azure에 할 일 앱을 게시할 때 사용 된 위치에 있습니다. 이 문서에서는 **미국 동부**를 사용했습니다.

설정할 **리소스 그룹** 원래 Azure에 할 일 앱을 게시할 때 사용 된 리소스 그룹에 있습니다.

설정할 **Azure Container Registry** 원래 Azure에 할 일 앱을 게시할 때 만든 azure 컨테이너 레지스트리 이름에 있습니다.

게시 대화 상자에서 **게시** 단추를 눌러 Azure의 할 일 앱을 업그레이드합니다.

선택 하 여 업그레이드의 진행률을 모니터링 합니다 **Service Fabric 도구** Visual Studio에서 창 **출력** 창입니다. 

이미지를 빌드하고 Azure Container Registry에 푸시된 후는 **상태에 대 한** 링크는 Azure portal에서 배포를 모니터링 하려면 클릭할 수 있는 출력에 표시 됩니다.

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