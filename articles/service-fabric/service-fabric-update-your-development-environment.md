<properties
   pageTitle="서비스 패브릭 개발 환경 업데이트"
   description="최신 런타임, SDK 및 도구를 사용하려면 서비스 패브릭 개발 환경을 업데이트합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2015"
   ms.author="seanmck"/>

# 서비스 패브릭 개발 환경 업데이트

 서비스 패브릭은 로컬 개발에 사용하기 위한 런타임, SDK 및 도구의 새로운 릴리스를 정기적으로 제공합니다. 이러한 릴리스를 사용하여 로컬 개발 환경을 지속적으로 업데이트하면 응용 프로그램을 로컬로 작성 및 테스트할 때 항상 최신 기능과 버그 수정, 성능 향상 기능에 액세스할 수 있습니다.

## 로컬 클러스터 정리

 서비스 패브릭에서는 현재 로컬 클러스터가 실행되는 동안 서비스 패브릭 런타임 업그레이드를 지원하지 않습니다. 새로 업그레이드를 하기 위해서는 먼저 로컬 클러스터를 정리해야 합니다.

 >[AZURE.NOTE]로컬 클러스터를 정리하면 배포된 모든 응용 프로그램 및 해당 데이터가 제거됩니다.

 다음과 같이 로컬 클러스터를 정리할 수 있습니다.


 1. 다른 PowerShell 창을 모두 닫고 관리자로 새 PowerShell 창을 시작합니다.

 2. `cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"`가 포함된 클러스터 설정 디렉토리로 이동합니다.

 3. `.\CleanCluster.ps1` 실행


## 런타임, SDK 및 도구 업데이트

 기존 클러스터를 성공적으로 정리하면 다음과 같이 업그레이드를 계속 진행할 수 있습니다.


 1. 웹 플랫폼 설치 관리자를 시작하여 [새 릴리스로 업데이트][1]합니다.

 2. 완료되면 관리자 권한으로 새 PowerShell 창을 실행하고 `cd "$ENV:ProgramFiles\Microsoft SDKs\ServiceFabric\ClusterSetup"`을 사용하여 클러스터 설치 디렉터리로 이동합니다.

 3. `.\DevClusterSetup.ps1`을 실행하여 로컬 클러스터를 설정합니다.

이것으로 끝입니다. 이제 Visual Studio를 시작하고 서비스 패브릭 응용 프로그램을 계속 빌드할 수 있습니다.

>[AZURE.NOTE]기본 프로젝트 구조가 이 릴리스에서 변경되었습니다. Visual Studio에서 기존 프로젝트를 열고 실행할 수 있습니다. 그러나 응용 프로그램을 빌드, 배포 또는 디버깅하는 데 문제가 발생하는 경우 새 프로젝트를 만들고 코드를 마이그레이션하는 것을 고려해 보세요.

 [1]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "WebPI 링크"

<!---HONumber=August15_HO7-->