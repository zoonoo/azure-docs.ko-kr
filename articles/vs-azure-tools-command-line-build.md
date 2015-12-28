<properties
   pageTitle="Azure에 대한 명령줄 빌드 "
   description="Azure에 대한 명령줄 빌드 "
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2015"
   ms.author="tarcher" />

# Azure에 대한 명령줄 빌드

## 개요

명령 프롬프트에서 MSBuild를 실행하 여 Azure 배포에 대한 패키지를 만들 수 있습니다. 디버깅, 스테이징 및 프로덕션에 대한 빌드를 구성하고 정의할 수 있으며 또한 빌드 프로세스의 일부를 자동화할 수 있습니다.


## Microsoft Build Engine (MSBuild)

Microsoft Build Engine (MSBuild)를 사용 하여 Visual Studio가 설치되지 않은 빌드 랩 환경에 제품을 빌드할 수 있습니다. MSBuild는 Microsoft에서 확장 가능하고 완전히 지원되는 프로젝트 파일에 대한 XML 형식을 사용합니다. 이 파일 형식으로 하나 이상의 플랫폼 및 구성에 대해 어떤 항목을 빌드해야 하는지 설명할 수 있습니다

명령 프롬프트에서 MSBuild를 실행할 수도 있으며 이 항목에서는 그 방법을 설명합니다. 명령 프롬프트에서 속성을 설정하여 프로젝트의 특정 구성을 빌드할 수 있습니다. 마찬가지로, MSBuild 명령이 빌드할 대상을 정의할 수 있습니다. 명령줄 매개 변수 및 MSBuild에 대한 자세한 내용은 [MSBuild 명령줄 참조](https://msdn.microsoft.com/library/ms164311.aspx)를 참조하세요.

## 설치

다음 절차에서 설명한 대로 MSBuild를 사용하여 Azure 패키지를 만들기 전에 먼저 소프트웨어 및 도구를 빌드 서버에 설치해야 합니다.

1. MSBuild가 포함된 .NET Framework 4 이상의 버전을 설치 합니다.

1. [Azure 작성 도구](http://go.microsoft.com/fwlink/?LinkId=394615)(MicrosoftAzureAuthoringTools-x64.msi 또는 MicrosoftAzureAuthoringTools-x86.msi 검색)를 설치합니다.

1. [ .NET 용 Azure 라이브러리](http://go.microsoft.com/fwlink/?LinkId=394616)(MicrosoftAzureLibsForNet-x64.msi 또는 MicrosoftAzureLibs-x86.msi 검색)를 설치합니다.

1. 다른 컴퓨터의 Visual Studio 설치에서 Microsoft.WebApplication.targets 파일을 복사합니다.

    파일은 디렉터리 C:\\Program Files (x86)\\MSBuild\\Microsoft\\Visual Studio\\v12.0\\WebApplications (v11.0 for Visual Studio 2012)에 위치하며 빌드 서버의 동일한 디렉터리에 해당 파일을 복사해야 합니다.

1. [Azure Tools for Visual Studio](http://go.microsoft.com/fwlink/?LinkId=394616)를 설치합니다.

    Visual Studio 2013 프로젝트를 빌드하려면 WindowsAzureTools.vs120.exe를 찾습니다.

## MSBuild 매개 변수

패키지를 만드는 가장 간단한 방법은`/t:Publish` 옵션으로 MSBuild를 실행하는 것 입니다. 기본적으로 이 명령은 프로젝트의 루트 폴더를 기준으로 디렉터리를 만듭니다(예: ProjectDir\\bin\\Configuration\\app.publish). Azure 프로젝트를 빌드하면 패키지 파일 자체와 함께 제공되는 구성 파일의 두 파일이 생성됩니다.

- Project.cspkg

- ServiceConfiguration.TargetProfile.cscfg

기본적으로 각 Azure 프로젝트에는 로컬(디버깅) 빌드용 서비스 구성 파일 하나와 클라우드(스테이징 또는 프로덕션) 빌드용 서비스 구성 파일 하나가 포함되지만 필요에 따라 서비스 구성 파일을 추가하거나 제거할 수 있습니다. Visual Studio 내에서 패키지를 빌드하면 패키지와 함께 포함할 서비스 구성 파일을 묻는 메시지가 표시됩니다. MSBuild를 사용하여 패키지를 빌드하면 로컬 서비스 구성 파일이 기본적으로 포함됩니다. 다른 서비스 구성 파일을 포함하려면 MSBuild 명령(`MSBuild /t:Publish /p:TargetProfile=ProfileName`)의 `TargetProfile`속성을 설정합니다.

저장된 패키지 및 구성 파일에 대해 다른 디렉터리를 사용하려는 경우 후행 백슬래시 구분 기호를 포함하는 `/p:PublishDir=Directory`옵션을 사용하여 경로를 설정합니다.

## 배포

패키지를 빌드한 후에 Azure에 배포할 수 있습니다. 이 과정을 보여주는 자습서는 Azure 웹사이트를 참조합니다. 이 프로세스를 자동화하는 방법에 대한 정보는 [Azure의 클라우드 서비스에 대한 지속적인 전송](../cloud-services/cloud-services-dotnet-continuous-delivery)을 참조하십시오.

<!---HONumber=AcomDC_1217_2015-->