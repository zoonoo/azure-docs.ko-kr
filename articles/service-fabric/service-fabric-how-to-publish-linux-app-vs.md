---
title: 원격 Linux 클러스터에 a.Net Core 앱 만들기 및 게시
description: Visual Studio에서 원격 Linux 클러스터를 대상으로 하는 .NET Core 앱 만들기 및 게시
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75614352"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Visual Studio를 사용하여 원격 Linux Service Fabric 클러스터를 대상으로 하는 .NET Core 애플리케이션 만들기 및 게시
Visual Studio 도구를 사용하면 Linux Service Fabric 클러스터를 대상으로 하는 Service Fabric .NET Core 애플리케이션을 개발하고 게시할 수 있습니다. Visual Studio에서 Linux Service Fabric 클러스터를 대상으로 하는 .NET Core 애플리케이션을 배포하려면 SDK 버전이 3.4 이상이어야 합니다.

> [!Note]
> Visual Studio에서는 Linux를 대상으로 하는 Service Fabric 애플리케이션 디버깅을 지원하지 않습니다.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>.NET Core를 대상으로 하는 Service Fabric 애플리케이션 만들기
1. **관리자** 권한으로 Visual Studio를 시작합니다.
2. **파일 -> 새로 만들기 -> 프로젝트** 를 사용하여 프로젝트를 만듭니다.
3. **새 프로젝트** 대화 상자에서 **클라우드 -> Service Fabric 애플리케이션** 을 선택합니다.
![create-application]
4. 애플리케이션 이름을 지정하고 **확인** 을 클릭합니다.
5. **새 Service Fabric 서비스** 페이지의 **.NET Core 섹션** 에서 만들려는 서비스 유형을 선택합니다.
![create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>원격 Linux 클러스터에 배포
1. 솔루션 탐색기에서 애플리케이션을 마우스 오른쪽 단추로 클릭하고 **빌드** 를 선택합니다.
![build-application]
2. 애플리케이션에 대한 빌드 프로세스가 완료되면 해당 서비스를 마우스 오른쪽 단추로 클릭하고 **csproj 파일** 편집을 선택합니다.
![edit-csproj]
3. 서비스가 **작업자 프로젝트 형식** 인 경우 UpdateServiceFabricManifestEnabled 속성을 True에서 **False** 로 편집합니다. 애플리케이션에 행위자 서비스가 없는 경우 4단계로 건너뜁니다.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> UpdateServiceFabricManifestEnabled를 False로 설정하면 빌드 중에 ServiceManifest.xml에 대한 업데이트를 사용하지 않도록 설정합니다. 서비스에 대한 추가, 제거 또는 이름 바꾸기와 같은 변경 내용은 ServiceManifest.xml에 반영되지 않습니다. 변경 내용이 있는 경우 ServiceManifest를 수동으로 업데이트하거나 일시적으로 UpdateServiceFabricManifestEnabled를 True로 설정하고 ServiceManifest.xml를 업데이트하는 서비스를 빌드한 후 이를 다시 False로 되돌려야 합니다.
>

4. RuntimeIndetifier를 win7-x64에서 서비스 프로젝트의 대상 플랫폼으로 업데이트합니다.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. ServiceManifest에서 진입점 프로그램을 업데이트하여 .exe를 제거합니다. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. 솔루션 탐색기에서 애플리케이션을 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다. **게시** 대화 상자가 나타납니다.
7. **연결 엔드포인트** 에서 대상으로 하려는 원격 Service Fabric Linux 클러스터에 대한 엔드포인트를 선택합니다.
![publish-application]

<!--Image references-->
[create-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[create-service]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[build-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publish-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>다음 단계
* [.NET Core를 사용하여 Service Fabric을 시작](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)하는 방법 알아보기
