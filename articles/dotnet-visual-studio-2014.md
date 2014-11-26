<properties linkid="dotnet-visual-studio-2014" urlDisplayName="Visual Studio 14 CTP2" pageTitle="Installing the Azure SDK 2.4 for Visual Studio 14 CTP2" metaKeywords="Visual Studio, Azure SDK" description="Install Azure SDK 2.4 and Visual Studio 14 CTP2" metaCanonical="" services="" documentationCenter="" title="Installing Azure SDK 2.4 for Visual Studio 14 CTP2" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen" />

## Visual Studio "14" CTP용 Azure SDK 2.4 설치

Visual Studio "14" CTP와 함께 Azure SDK 2.4 for .NET을 설치하려면 다음 단계를 따르세요. 이 절차에서는 Visual Studio "14" CTP와 함께 Azure 개발용 SDK, 기본 도구 및 확장 도구를 설치하며, 다른 버전의 Visual Studio에는 이 절차를 사용할 수 없습니다.

**참고**: Azure SDK 2.4는 Visual Studio "14" CTP1과 호환되지 않습니다.

Azure SDK 2.4 for .NET을 설치하려면 다음 단계를 따르세요.

1.  최신 [Visual Studio "14" CTP][Visual Studio "14" CTP]를 설치합니다.

2.  다음 목록의 링크를 순서대로 사용하여 Azure SDK의 각 구성 요소를 설치합니다. 다음 각 구성 요소의 x86 또는 x64 버전을 선택합니다.

    -   Azure Authoring Tools: [WindowsAzureAuthoringTools-x86.msi][WindowsAzureAuthoringTools-x86.msi] 또는 [WindowsAzureAuthoringTools-x64.msi][WindowsAzureAuthoringTools-x64.msi]
    -   Azure 계산 에뮬레이터: [WindowsAzureEmulator-x86.exe][WindowsAzureEmulator-x86.exe] 또는 [WindowsAzureEmulator-x64.exe][WindowsAzureEmulator-x64.exe]
    -   Azure 클라이언트 라이브러리: [WindowsAzureLibsForNet-x86.msi][WindowsAzureLibsForNet-x86.msi] 또는 [WindowsAzureLibsForNet-x64.msi][WindowsAzureLibsForNet-x64.msi]
    -   저장소 에뮬레이터: [WindowsAzureStorageEmulator.msi][WindowsAzureStorageEmulator.msi]. 로컬 SQL 데이터베이스와 관련된 경고가 표시되면 x86 의 경우 [여기][여기]에서, x64의 경우 [여기][1]에서 SQL Server LocalDB 11.0을 설치합니다.
    -   Azure Tools for Visual Studio: [WindowsAzureTools.vs140.exe][WindowsAzureTools.vs140.exe]

    </p>

## 알려진 문제

1.  Visual Studio 2013가 설치된 컴퓨터에 Visual Studio "14" CTP2를 설치하는 경우 Visual Studio "14" CTP2에서 모바일 서비스를 시작할 수 없습니다. 이 문제를 해결하려면 모바일 서비스 프로젝트에서 다음 어셈블리에 대한 참조를 추가하세요.

    -   packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
    -   packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2.  Azure 웹 사이트 및 모바일 서비스에 대한 원격 디버깅은 Visual Studio "14" CTP2에서 작동하지 않습니다.

## 릴리스 정보

Azure SDK 2.4에 대한 [릴리스 정보][릴리스 정보](영문)를 참조하세요.

  [Visual Studio "14" CTP]: http://go.microsoft.com/fwlink/p/?LinkId=400776
  [WindowsAzureAuthoringTools-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400892
  [WindowsAzureAuthoringTools-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400893
  [WindowsAzureEmulator-x86.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400894
  [WindowsAzureEmulator-x64.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400895
  [WindowsAzureLibsForNet-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400896
  [WindowsAzureLibsForNet-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400897
  [WindowsAzureStorageEmulator.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400904
  [여기]: http://go.microsoft.com/fwlink/p/?LinkId=400778
  [1]: http://go.microsoft.com/fwlink/p/?LinkId=400779
  [WindowsAzureTools.vs140.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400903
  [릴리스 정보]: http://go.microsoft.com/fwlink/?LinkId=507517
