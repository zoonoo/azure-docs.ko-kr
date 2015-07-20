<properties
   pageTitle="서비스 패브릭 개발 환경 설정"
   description="서비스 패브릭 런타임, SDK 및 도구를 설치하고 로컬 개발 클러스터를 만듭니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/04/2015"
   ms.author="seanmck"/>

# 서비스 패브릭 개발 환경 설정
 이 문서에서는 런타임, SDK 및 도구의 설치 및 로컬 클러스터 설정을 포함하여 [서비스 패브릭][1] 앱을 빌드하기 시작할 때 필요한 모든 것을 다룹니다.

## 필수 조건
### 지원되는 운영 체제 버전
다음 운영 체제 버전이 지원됩니다.

- Windows 8/8.1
- Windows Server 2012 R2
- Windows 10 기술 미리 보기

### Visual Studio 2015

서비스 패브릭 미리 보기 1용 도구는 [여기][2]에서 찾을 수 있는 Visual Studio 2015 RC에 종속되어 있습니다.

> [AZURE.NOTE]지원되는 OS 버전 중 하나가 실행되고 있지 않거나 PC에 Visual Studio 2015 RC를 설치하지 않으려는 경우는 VM 갤러리의 이미지를 사용하여 Windows Server 2012 R2와 Visual Studio 2015가 미리 설치된 [Azure 가상 컴퓨터를 설정][3]할 수 있습니다.

## 런타임, SDK 및 도구 설치

서비스 패브릭 구성 요소는 웹 플랫폼 설치 관리자를 사용하여 설치됩니다. 다음 지침에 따라 설치하세요.

1. [여기][4]를 클릭하여 웹 플랫폼 설치 관리자를 통해 SDK를 다운로드합니다.

2. 설치를 클릭하여 설치 프로세스를 시작합니다.

3. EULA를 검토하고 동의합니다.

설치는 자동으로 진행됩니다.

## PowerShell 스크립트 실행 활성화

서비스 패브릭은 로컬 개발 클러스터를 만들고 Visual Studio에서 응용 프로그램을 배포하기 위해 Windows PowerShell 스크립트를 사용합니다. 기본적으로 Windows에서는 이러한 스크립트의 실행을 차단합니다. 따라서 이러한 스크립트를 사용하려면 PowerShell 실행 정책을 수정해야 합니다. 관리자로 PowerShell을 열고 다음 명령을 입력합니다.

    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser


## 로컬 클러스터 설치 및 시작
로컬 클러스터는 단일 개발 컴퓨터의 프로덕션 환경에서 최종적으로 사용할 다중 컴퓨터 토폴로지를 나타냅니다. 로컬 클러스터를 설정하려면 다음 단계를 따르세요.


1. 다른 PowerShell 창을 모두 닫고 관리자로 새 PowerShell 창을 시작합니다.

2. `cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"`가 포함된 클러스터 설정 디렉토리로 이동합니다.

3. `.\DevClusterSetup.ps1` 실행

곧 노드 정보와 함께 클러스터가 성공적으로 만들어졌는지 확인하는 메시지가 표시됩니다. 경우에 따라 서비스 패브릭 호스트 서비스 및 이름 서비스를 시작하는 동안 경고가 표시될 수도 있습니다. 이는 정상적인 것이며 뒤이어 일시적으로 클러스터에 대한 몇 가지 기본 정보가 표시됩니다.

> [AZURE.NOTE]사용자의 로컬 클러스터는 Azure에서 실행되는 것과 정확하게 동일한 런타임을 사용합니다. 또한 어떤 방식으로든 시뮬레이션 또는 에뮬레이트되지 않습니다. 유일한 차이점은 Azure처럼 여러 컴퓨터에 노드가 분산되는 것이 아니라 단일 컴퓨터에서 모든 노드가 실행된다는 점입니다.

## 클러스터 설치 유효성 검사

SDK와 함께 제공되는 서비스 패브릭 탐색기 도구를 사용하여, 클러스터가 성공적으로 만들어졌는지 확인할 수 있습니다.

1. `. "$env:ProgramW6432\Microsoft SDKs\Service Fabric\Tools\ServiceFabricExplorer\ServiceFabricExplorer.exe"`를 실행하여 서비스 패브릭 탐색기를 시작합니다.

2. 왼쪽 위 모퉁이에서 Onebox/로컬 클러스터 노드를 확장합니다.

3. 응용 프로그램 및 노드 보기가 녹색인지 확인합니다.

녹색이 아닌 요소가 있거나 오류가 표시되면 잠시 기다렸다가 새로 고침 단추를 클릭합니다. 여전히 문제가 있으면 [설치 문제 해결 단계](service-fabric-troubleshoot-local-cluster-setup.md)를 참조하세요.

## 다음 단계
개발 환경을 설정했으므로 앱의 빌드 및 실행을 시작할 수 있습니다.

- [프로그래밍 모델에 대해 알아보기: 신뢰할 수 있는 행위자 및 신뢰할 수 있는 서비스](service-fabric-choose-framework.md)
- [신뢰할 수 있는 서비스 API 시작](service-fabric-reliable-services-quick-start.md)
- [신뢰할 수 있는 행위자 API 시작](service-fabric-reliable-actors-get-started.md)
- [GitHub의 서비스 패브릭 샘플 확인](https://github.com/azure/servicefabric-samples)
- [서비스 패브릭 탐색기를 사용하여 클러스터 시각화](service-fabric-visualizing-your-cluster.md)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "서비스 패브릭 캠페인 페이지"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[3]: http://blogs.msdn.com/b/visualstudioalm/archive/2014/06/04/visual-studio-14-ctp-now-available-in-the-virtual-machine-azure-gallery.aspx "Azure VM"
[4]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "WebPI 링크"
 

<!---HONumber=July15_HO2-->