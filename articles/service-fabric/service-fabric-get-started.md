<properties
   pageTitle="개발 환경 설정 | Microsoft Azure"
   description="런타임, SDK 및 도구를 설치하고 로컬 개발 클러스터를 만듭니다. 이 설정을 마치면 응용 프로그램을 빌드할 수 있습니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/17/2015"
   ms.author="seanmck"/>

# 개발 환경 준비
 개발 컴퓨터에서 [서비스 패브릭 응용 프로그램][1]을 빌드 및 실행하려면 런타임, SDK, 도구를 설치하고 로컬 클러스터를 설정해야 합니다.

## 필수 조건
### 지원되는 운영 체제 버전
다음 운영 체제 버전이 지원됩니다.

- Windows 8/8.1
- Windows Server 2012 R2
- Windows 10

### Visual Studio 2015

서비스 패브릭용 도구는 [여기][2]에서 찾을 수 있는 Visual Studio 2015에 종속되어 있습니다.

> [AZURE.NOTE]지원되는 OS 버전 중 하나가 실행되고 있지 않거나 PC에 Visual Studio 2015를 설치하지 않으려는 경우는 Azure 가상 컴퓨터의 이미지를 사용하여 Windows Server 2012 R2와 Visual Studio 2015가 미리 설치된 Azure 가상 컴퓨터를 설정할 수 있습니다.

## 런타임, SDK 및 도구 설치

서비스 패브릭 구성 요소는 웹 플랫폼 설치 관리자를 사용하여 설치됩니다. 다음 지침에 따라 설치하세요.

1. 웹 플랫폼 설치 관리자를 사용하여 [SDK를 다운로드][3]합니다.

2. **설치**를 클릭하여 설치 프로세스를 시작합니다.

3. EULA를 검토하고 동의합니다.

설치는 자동으로 진행됩니다.

## PowerShell 스크립트 실행 활성화

서비스 패브릭은 로컬 개발 클러스터를 만들고 Visual Studio에서 응용 프로그램을 배포하기 위해 Windows PowerShell 스크립트를 사용합니다. 기본적으로 Windows에서는 이러한 스크립트의 실행을 차단합니다. 따라서 이러한 스크립트를 사용하려면 PowerShell 실행 정책을 수정해야 합니다. 관리자로 PowerShell을 열고 다음 명령을 입력합니다.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## 다음 단계
개발 환경을 설정했으므로 앱의 빌드 및 실행을 시작할 수 있습니다.

- [Visual Studio에서 서비스 패브릭 응용 프로그램 처음 만들기](service-fabric-create-your-first-application-in-visual-studio.md)
- [로컬 클러스터에서 응용 프로그램을 배포하고 관리하는 방법 알아보기](service-fabric-get-started-with-a-local-cluster.md)
- [프로그래밍 모델에 대해 알아보기: 신뢰할 수 있는 행위자 및 신뢰할 수 있는 서비스](service-fabric-choose-framework.md)
- [GitHub의 서비스 패브릭 샘플 확인](https://aka.ms/servicefabricsamples)
- [서비스 패브릭 탐색기를 사용하여 클러스터 시각화](service-fabric-visualizing-your-cluster.md)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "서비스 패브릭 캠페인 페이지"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[3]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "WebPI 링크"

<!---HONumber=AcomDC_1125_2015-->