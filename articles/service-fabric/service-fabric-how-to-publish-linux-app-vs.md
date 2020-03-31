---
title: 원격 리눅스 클러스터에 a.Net 핵심 앱 만들기 및 게시
description: Visual Studio에서 원격 Linux 클러스터를 대상으로 하는 .Net Core 앱 만들기 및 게시
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614352"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Visual Studio를 사용하여 원격 Linux 서비스 패브릭 클러스터를 대상으로 하는 .Net Core 응용 프로그램을 만들고 게시합니다.
Visual Studio 툴링을 사용하면 Linux 서비스 패브릭 클러스터를 대상으로 하는 서비스 패브릭 .Net Core 응용 프로그램을 개발하고 게시할 수 있습니다. Visual Studio에서 Linux 서비스 패브릭 클러스터를 대상으로 하는 .Net Core 응용 프로그램을 배포하려면 SDK 버전이 3.4 이상이어야 합니다.

> [!Note]
> Visual Studio는 Linux를 대상으로 하는 서비스 패브릭 응용 프로그램을 디버깅하는 것을 지원하지 않습니다.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>.Net 코어를 대상으로 하는 서비스 패브릭 응용 프로그램 만들기
1. **관리자** 권한으로 Visual Studio를 시작합니다.
2. 파일 >**>프로젝트를**사용하여 프로젝트를 만듭니다.
3. 새 **프로젝트** 대화 상자에서 **클라우드 > 서비스 패브릭 응용 프로그램을**선택합니다.
![만들기 응용 프로그램]
4. 응용 프로그램의 이름을 지정하고 **확인을**클릭합니다.
5. 새 **서비스 패브릭 서비스** 페이지에서 **.Net Core 섹션에서**만들 서비스 유형을 선택합니다.
![서비스 만들기]

## <a name="deploy-to-a-remote-linux-cluster"></a>원격 Linux 클러스터에 배포
1. 솔루션 탐색기에서 응용 프로그램을 마우스 오른쪽 단추로 클릭하고 **빌드를**선택합니다.
![빌드 응용 프로그램]
2. 응용 프로그램의 빌드 프로세스가 완료되면 서비스를 마우스 오른쪽 단추로 클릭하고 **csproj 파일을**편집합니다.
![편집 csproj]
3. 서비스가 **행위자 프로젝트 유형인**경우 UpdateServiceFabricManifestEnabled 속성을 True에서 **False로** 편집합니다. 응용 프로그램에 행위자 서비스가 없는 경우 4단계로 건너뜁니다.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> UpdateServiceFabricManifest를 false로 설정하면 빌드 중에 ServiceManifest.xml에 대한 업데이트를 비활성화합니다. 서비스에 추가, 제거 또는 이름 바꾸기와 같은 변경 사항은 ServiceManifest.xml에 반영되지 않습니다. 변경 사항이 있는 경우 ServiceManifest를 수동으로 업데이트하거나 일시적으로 UpdateServiceFabricManifestEnabled를 true로 설정하고 ServiceManifest.xml을 업데이트한 다음 false로 되돌리는 서비스를 빌드해야 합니다.
>

4. RuntimeIndetifier를 win7-x64에서 서비스 프로젝트의 대상 플랫폼으로 업데이트합니다.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. ServiceManifest에서 엔트리포인트 프로그램을 업데이트하여 .exe를 제거합니다. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. 솔루션 탐색기에서 응용 프로그램을 마우스 오른쪽 단추로 클릭하고 **게시를**선택합니다. **게시** 대화 상자가 나타납니다.
7. **연결 끝점에서**대상으로 지정하려는 원격 서비스 패브릭 Linux 클러스터의 끝점을 선택합니다.
![게시 응용 프로그램]

<!--Image references-->
[만들기 응용 프로그램]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[서비스 만들기]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[빌드 응용 프로그램]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[편집 csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[게시 응용 프로그램]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>다음 단계
* [.Net 코어를 통해 서비스 패브릭을 시작하는 방법에](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/) 대해 자세히 알아보기
