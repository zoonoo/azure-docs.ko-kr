---
title: 만들기 및.NET Core를 게시 하는 방법에 대 한 자세한 원격 Azure Service Fabric Linux 클러스터에 응용 프로그램 | Microsoft Docs
description: 만들기 및 게시.NET Core Visual Studio에서 원격 Linux 클러스터를 대상으로 하는 앱
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: 46d76edbe8cede12e8c7811f43c28a65c1ebaed0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078665"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Visual Studio를 사용 하 여.NET Core를 게시 하는 원격 Linux Service Fabric 클러스터를 대상으로 하는 응용 프로그램
Visual Studio를 사용 하 여 도구를 개발 하 고 게시할 수 Service Fabric.NET Core 응용 프로그램을 Linux Service Fabric 클러스터를 대상으로 합니다. SDK 버전 3.4 여야 합니다. 위의 배포는.NET Core 또는 Linux Service Fabric을 대상으로 하는 응용 프로그램 Visual Studio에서 클러스터.

> [!Note]
> Visual Studio Linux를 대상으로 하는 Service Fabric 응용 프로그램 디버깅을 지원 하지 않습니다.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>.NET Core를 대상으로 하는 Service Fabric 응용 프로그램 만들기
1. **관리자** 권한으로 Visual Studio를 시작합니다.
2. 사용 하 여 프로젝트를 만듭니다 **파일-> 새로 만들기-> 프로젝트**합니다.
3. 에 **새 프로젝트** 대화 상자에서 선택 **-> Service Fabric 응용 프로그램을 클라우드**합니다.
![create-application]
4. 응용 프로그램 이름을 지정 하 고 클릭 **확인**합니다.
5. 에 **새 Service Fabric 서비스** 페이지에서 아래에 만들려는 원하는 서비스 유형을 선택 합니다 **.NET Core 섹션**.
![create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>원격 Linux 클러스터에 배포
1. 선택한 응용 프로그램의 솔루션 탐색기에서 마우스 오른쪽 단추로 클릭 **빌드**합니다.
![build-application]
2. 응용 프로그램에 대 한 빌드 프로세스가 완료 되 면 서비스를 마우스 오른쪽 단추로 클릭 하 고 편집을 선택 합니다 **csproj 파일**합니다.
![edit-csproj]
3. True 이면에서 UpdateServiceFabricManifestEnabled 속성 편집 **False** 서비스의 경우는 **행위자 프로젝트 형식**합니다. 응용 프로그램에 행위자 서비스를 찾을 수 없는 경우에 4 단계로 건너뜁니다.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> UpdateServiceFabricManifestEnabled을 false로 설정 된 ServiceManifest.xml에 대 한 업데이트를 빌드하는 동안 비활성화 됩니다. 이러한 변경으로 추가, 제거 또는 서비스의 이름을 반영 되지 않습니다 ServiceManifest.xml에서. 모든 변경 된 경우 있습니다 ServiceManifest 일시적으로 또는 수동으로 설정 UpdateServiceFabricManifestEnabled true ServiceManifest.xml 업데이트 되며 되돌린 후 서비스를 구축 하는 업데이트 하거나 백업 해야 false로 합니다.
>

4. 서비스 프로젝트의 대상 플랫폼에 win7-x64에서 RuntimeIndetifier를 업데이트 합니다.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. ServiceManifest에.exe 제거할 entrypoint 프로그램을 업데이트 합니다. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. 솔루션 탐색기에서 마우스 오른쪽 단추로 클릭 선택한 응용 프로그램 **게시**합니다. **게시** 대화 상자가 나타납니다.
7. **연결 끝점**를 대상으로 하려는 원격 Service Fabric Linux 클러스터에 대 한 끝점을 선택 합니다.
![publish-application]

<!--Image references-->
[create-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[create-service]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[build-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publish-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>다음 단계
* 에 대 한 자세한 [.NET Core를 사용 하 여 Service Fabric 시작](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
