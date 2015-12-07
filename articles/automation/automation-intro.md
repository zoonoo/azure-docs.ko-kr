<properties
	pageTitle="Azure 자동화 정의 | Microsoft Azure"
	description="Azure 자동화가 제공하는 가치 및 일반적인 질문에 대한 답을 얻으므로 사용자가 Runbook 및 Azure 자동화 DSC를 사용하여 시작할 수 있습니다."
	services="automation"
	documentationCenter=""
	authors="SnehaGunda"
	manager="stevenka"
	editor=""/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="11/05/2015"
	ms.author="bwren;sngun"/>

# Azure 자동화 개요


Microsoft Azure 자동화를 통해 사용자는 일반적으로 클라우드 엔터프라이즈 환경에서 장시간 동안 수동으로 실행되며 오류가 발생하기 쉬운 자주 반복되는 작업을 자동화할 수 있습니다. 시간을 절약하며 일반 관리 작업의 안정성을 향상시키고 정기적으로 자동으로 수행되도록 예약합니다. Runbook을 사용하는 프로세스를 자동화하거나 원하는 상태 구성을 사용하여 구성 관리를 자동화할 수 있습니다. 이 문서에서는 Azure 자동화의 간단한 개요 및 일반적인 질문에 대한 답변을 제공합니다. 이 라이브러리의 다른 문서를 참조하여 다른 항목에 대한 자세한 내용을 얻을 수 있습니다.


## Runbook을 사용하여 프로세스 자동화

Runbook은 Azure 자동화에서 일부 자동화 된 프로세스를 수행하는 작업의 집합입니다. 가상 컴퓨터를 시작하고 로그 항목을 만드는 것과 같은 단순한 프로세스이거나 여러 리소스 또는 여러 클라우드와 온-프레미스 환경에 걸쳐 복잡한 프로세스를 수행하는 다른 작은 Runbook을 결합하는 복잡한 Runbook이 있을 수 있습니다.

예를 들어 SQL 데이터베이스가 서버에 연결, 데이터베이스에 연결, 데이터베이스의 현재 크기를 가져오기, 임계값을 초과하는지 여부 및 자르기와 사용자에게 알리기와 같은 여러 단계를 포함하는 최대 크기에 도달하는 경우 해당 SQL 데이터베이스를 자르기 위해 기존의 수동 프로세스가 있을 수 있습니다. 이러한 각 단계를 수동으로 수행하는 대신 단일 프로세스로 이러한 작업을 모두 수행하는 runbook을 만들 수 있습니다. Runbook을 실행하고 SQL Server 이름, 데이터베이스 이름, 받는 사람 메일 등의 필수 정보를 입력한 후에 프로세스가 완료될 때까지 기다리기만 하면 됩니다.


## Runbook 자동화가 할 수 있는것은 무엇입니까?

Azure 자동화의 Runbook은 Windows PowerShell 또는 Windows PowerShell 워크플로를 기반으로 하기 때문에 PowerShell의 모든 기능을 수행할 수 있습니다. API가 있는 응용 프로그램이거나 서비스라면 runbook과 함께 작업할 수 있습니다. 응용 프로그램에 대해 PowerShell 모듈이 있다면 모듈을 Azure 자동화로 로드하고 Runbook에 있는 cmdlet을 포함할 수 있습니다. Azure 자동화 Runbook은 Azure cloud에서 실행되고 클라우드에서 액세스할 수 있는 클라우드 리소스 혹은 외부 리소스에 액세스할 수 있습니다. [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)를 사용하여 Runbook을 로컬 데이터 센터에서 실행하여 로컬 리소스를 관리할 수 있습니다.


## 커뮤니티에서 Runbook 가져오기

[Runbook 갤러리](http://msdn.microsoft.com/library/azure/dn781422.aspx)는 Microsoft Runbook 및 사용자 환경에서 그대로 사용하거나 용도에 맞게 사용자 지정한 커뮤니티 Runbook을 포함합니다. 사용자 고유의 runbook을 만드는 방법에 학습에 대한 참조로 유용할 수도 있습니다. 다른사람들에게도 유용할 것 같은 사용자 고유의 runbook을 갤러리에 제공 할 수도 있습니다.


## Azure 자동화를 사용하여 Runbook 만들기 

처음부터 [고유한 Runbook 만들기](http://msdn.microsoft.com/library/azure/dn643637.aspx)를 사용하거나 [Runbook 갤러리](http://msdn.microsoft.com/library/azure/dn781422.aspx)에서 사용자 요구 사항으로 Runbook을 수정할 수 있습니다. 요구 사항 및 PowerShell 환경에 따라 선택할 수 있는 세 가지 [Runbook 형식](automation-runbook-types.md)이 있습니다. PowerShell 코드로 직접 작업하려는 경우, 오프라인으로 편집하거나 Azure 포털에서 [텍스트 편집기를 사용](http://msdn.microsoft.com/library/azure/dn879137.aspx)하여 편집하는 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 또는 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)을 사용할 수 있습니다. 기본 코드에 노출되지 않고 Runbook을 편집하려는 경우, Azure Preview 포털에서 [그래픽 편집기](automation-graphical-authoring-intro.md)를 사용하여 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)을 만들 수 있습니다.

읽기보다 비디오를 시청하기가 편하신가요? 2015년 5월 Microsoft Ignite 세션에서 발표된 아래 동영상을 살펴보세요. 참고: 이 동영상에 언급된 개념 및 기능은 정확하지만 이 동영상이 녹화된 이후에 Azure 자동화는 많이 발전되었으며 현재 Azure 포털에서 보다 광범위한 UI를 제공하며 추가 기능을 지원합니다.

> [AZURE.VIDEO microsoft-ignite-2015-automating-operational-and-management-tasks-using-azure-automation]


## 원하는 상태 구성을 사용하여 구성 관리 자동화 

[PowerShell DSC](https://technet.microsoft.com/library/dn249912.aspx)는 선언적 PowerShell 구문을 사용하여 실제 호스트 및 가상 컴퓨터에 대한 구성을 관리, 배포 및 적용할 수 있도록 해주는 관리 플랫폼입니다. 대상 컴퓨터가 자동으로 검색하고 적용하는 중앙 DSC 끌어오기 서버에서 구성을 정의할 수 있습니다. DSC는 구성 및 리소스 관리를 사용할 수 있는 PowerShell cmdlet의 집합을 제공합니다.

[Azure 자동화 DSC](automation-dsc-overview.md)는 엔터프라이즈 환경에 필요한 서비스를 제공하는 PowerShell DSC에 대한 클라우드 기반 솔루션입니다. Azure 자동화에서 DSC 리소스를 관리하고 Azure 클라우드의 DSC 끌어오기 서버에서 이를 검색하는 가상 또는 실제 컴퓨터에 구성을 적용할 수 있습니다. 또한 노드가 할당된 구성에서 파생되는 시기 및 새 구성이 적용되는 시기와 같은 중요한 이벤트를 알려주는 보고 서비스를 제공합니다.


## Azure 자동화를 사용하여 고유한 DSC 구성 만들기

[DSC 구성](automation-dsc-overview.md#azure-automation-dsc-terms)은 노드의 원하는 상태를 지정합니다. 여러 노드는 동일한 구성을 적용하여 동일한 상태를 유지하도록 할 수 있습니다. 로컬 컴퓨터에서 텍스트 편집기를 사용하여 구성을 만들고 컴파일하고 노드에 적용할 수 있는 Azure 자동화에 가져올 수 있습니다.


## 모듈 및 구성 가져오기 

[PowerShell 갤러리](http://www.powershellgallery.com/)의 Runbook 및 DSC 구성에서 사용할 수 있는 cmdlet을 포함하는 [PowerShell 모듈](automation-runbook-gallery.md#modules-in-powershell-gallery)을 가져올 수 있습니다. Azure Preview 포털에서 이 갤러리를 실행하여 Azure 자동화에 직접 모듈을 가져오거나 수동으로 다운로드하고 가져올 수 있습니다. Azure 포털에서 모듈을 직접 설치할 수는 없지만 다운로드하여 다른 모듈처럼 설치할 수 있습니다.


## Azure 자동화의 실제 응용 프로그램 예제 

다음은 Azure 자동화를 사용하여 자동화할 수 있는 시나리오 종류의 몇 가지 예입니다.

* 여러 Azure 구독에 가상 컴퓨터를 만들고 복사합니다. 
* 로컬 컴퓨터에서 Azure Blob 저장소 컨테이너로 파일 복사본을 예약합니다. 
* 서비스 공격이 거부된 것이 감지되면 클라이언트에서 요청 거부와 같은 보안 기능을 자동화합니다. 
* 구성된 보안 정책을 사용하여 지속적으로 컴퓨터를 정렬하도록 합니다.
* 클라우드 간 및 온-프레미스 인프라에 응용 프로그램 코드의 지속적인 배포를 관리합니다. 
* 랩 환경에 Azure의 Active Directory 포리스트를 빌드합니다. 
* DB가 최대 크기에 도달하면 SQL 데이터베이스의 테이블을 자릅니다. 
* Azure 웹 사이트의 환경 설정을 원격으로 업데이트합니다. 


## Azure 자동화는 다른 자동화 도구와 어떤 관계가 있습니까?

[SMA(서비스 관리 자동화)](http://technet.microsoft.com/library/dn469260.aspx)는 사설 클라우드의 관리 작업을 자동화하는 데 사용됩니다. [Microsoft Azure Pack](https://www.microsoft.com/ko-KR/server-cloud/)의 구성 요소로 데이터 센터에 로컬로 설치됩니다. SMA와 Azure 자동화는 Windows PowerShell 및 Windows PowerShell 워크플로를 기반으로 하는 동일한 Runbook을 사용하지만, SMA는 [그래픽 Runbook](automation-graphical-authoring-intro.md)을 지원하지 않습니다.

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)는 온-프레미스 리소스의 자동화에 사용됩니다. Azure 자동화 및 서비스 관리 자동화와 다른 runbook 형식을 사용하고 스크립팅 필요없이 runbook을 만드는 그래픽 인터페이스가 있습니다. 해당 runbook은 Ochestrator에 대해 구체적으로 적힌 통합 팩의 활동들로 구성됩니다.


## 자세한 정보는 어디서 얻을 수 있습니까? 

다양한 리소스를 통해 Azure 자동화 및 고유한 Runbook 만들기에 대한 자세한 내용을 확인할 수 있습니다.

* 현재 위치는 **Azure 자동화 라이브러리**입니다. 이 문서에서는 Azure 자동화를 구성 및 관리하고 사용자 고유의 Runbook을 만드는 방법에 대한 전체 설명서를 제공합니다. 
* [Azure PowerShell cmdlet](http://msdn.microsoft.com/library/jj156055.aspx)은 Windows PowerShell을 사용하여 Azure 작업을 자동화하는 방법에 대한 정보를 제공합니다. Runbook에서는 이러한 cmdlet을 사용하여 Azure 리소스로 작업합니다. 
* [관리 블로그](https://azure.microsoft.com/ko-KR/blog/tag/azure-automation/)는 Azure 자동화 및 Microsoft의 기타 관리 기술에 대한 최신 정보를 제공합니다. Azure 자동화 팀에서 제공하는 최신 정보를 받으려면 Azure 자동화 블로그를 구독하세요. 
* [자동화 포럼](http://go.microsoft.com/fwlink/p/?LinkId=390561)에서는 Microsoft 및 자동화 커뮤니티에서 다루는 Azure 자동화에 대한 질문을 게시할 수 있습니다. 
* [Azure 자동화 cmdlet](https://msdn.microsoft.com/library/mt244122.aspx)은 관리 작업을 자동화하는 방법에 대한 정보를 제공합니다. 자동화 계정, 자산, Runbook, DSC을 관리하는 cmdlet을 포함합니다.


## 피드백을 제공할 수 있습니까? 

**사용자 의견을 보내 주세요!** Azure 자동화 Runbook 솔루션 또는 통합 모듈을 찾고 있는 경우 스크립트 센터에 스크립트 요청을 게시하세요. Azure 자동화에 대한 의견이나 기능 요청이 있는 경우 [사용자 음성](http://feedback.windowsazure.com/forums/34192--general-feedback)에 게시하세요. 감사합니다.

<!---HONumber=AcomDC_1125_2015-->