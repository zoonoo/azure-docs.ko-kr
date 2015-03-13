<properties 
	pageTitle="Visual Studio 14 CTP2용 Azure SDK 2.4 설치" 
	description="Azure SDK 2.4 및 Visual Studio 14 CTP2 설치" 
	services="visual-studio-online" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="visual-studio-online" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>
# Visual Studio "14" CTP용 Azure SDK 2.4 설치

Visual Studio "14" CTP와 함께 Azure SDK 2.4 for .NET을 설치하려면 다음 단계를 따르세요. 이 절차에서는 Visual Studio "14" CTP와 함께 Azure 개발용 SDK, 기본 도구 및 확장 도구를 설치하며, 다른 버전의 Visual Studio에는 이 절차를 사용할 수 없습니다.

**참고**: Azure SDK 2.4는 Visual Studio "14" CTP1과 호환되지 않습니다.

Azure SDK 2.4 for .NET을 설치하려면 다음 단계를 따르세요.

1. 최신 [Visual Studio "14" CTP](http://go.microsoft.com/fwlink/p/?LinkId=400776)를 설치합니다.

2. 다음 목록의 링크를 순서대로 사용하여 Azure SDK의 각 구성 요소를 설치합니다. 다음 각 구성 요소의 x86 또는 x64 버전을 선택합니다.

       <ul>
        <li>Azure 작성 도구: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400892">MicrosoftAzureAuthoringTools-x86.msi</a> 또는 <a href="http://go.microsoft.com/fwlink/p/?LinkId=400893">MicrosoftAzureAuthoringTools-x64.msi</a></li>
       <li>Azure 계산 에뮬레이터: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400894">MicrosoftAzureEmulator-x86.exe</a> 또는 <a href="http://go.microsoft.com/fwlink/p/?LinkId=400895">MicrosoftAzureEmulator-x64.exe</a></li>
       <li>Azure 클라이언트 라이브러리: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400896">MicrosoftAzureLibsForNet-x86.msi</a> 또는 <a href="http://go.microsoft.com/fwlink/p/?LinkId=400897">MicrosoftAzureLibsForNet-x64.msi</a></li>
       <li>저장소 에뮬레이터: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400904">MicrosoftAzureStorageEmulator.msi</a>.                            로컬 SQL 데이터베이스와 관련된 경고가 표시되면 x86 의 경우 <a href="http://go.microsoft.com/fwlink/p/?LinkId=400778">여기</a>에서, x64의 경우 <a href="http://go.microsoft.com/fwlink/p/?LinkId=400779">여기</a>에서 SQL Server LocalDB 11.0을 설치합니다.</li><li> Azure Tools for Visual Studio: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400903">WindowsAzureTools.vs140.exe</a>.</li></ul>

## 알려진 문제

1. Visual Studio 2013이 설치된 컴퓨터에 Visual Studio "14" CTP2를 설치하는 경우 Visual Studio "14" CTP2에서 모바일 서비스를 시작할 수 없습니다. 이 문제를 해결하려면  모바일 서비스 프로젝트에서 다음 어셈블리에 대한 참조를 추가하세요.

	* packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
	* packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2. Azure 웹 사이트 및 모바일 서비스에 대한 원격 디버깅은 Visual Studio "14" CTP2에서 작동하지 않습니다.

## 릴리스 정보

Azure SDK 2.4에 대한 [릴리스 정보](http://go.microsoft.com/fwlink/?LinkId=507517)(영문)를 참조하세요.

<!--HONumber=46--> 
