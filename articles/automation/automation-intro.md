<properties
	pageTitle="Azure 자동화란?"
	description="Azure 자동화가 제공하는 가치 및 일반적인 질문에 대한 답을 얻고, 사용자가 runbook을 생성 및 사용을 시작할 수 있도록 합니다."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor=""/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="07/06/2015"
	ms.author="bwren"/>

# Azure 자동화 정의

Microsoft Azure 자동화를 통해 사용자는 일반적으로 클라우드 엔터프라이즈 환경에서 장시간 동안 수동으로 실행되며 오류가 발생하기 쉬운 자주 반복되는 작업을 자동화할 수 있습니다. 이 문서는 Azure 자동화에 대 한 일반적인 질문에 대한 간략한 답변을 제공합니다. 이 라이브러리의 다른 문서를 참조하여 다른 항목에 대한 자세한 내용을 얻을 수 있습니다.

## Azure 자동화는 어떤 가치를 제공합니까?

Azure 자동화는 시간을 절약하고 클라우드 및 엔터프라이즈 환경에서 수행하는 일반적인 관리 작업의 안정성을 향상 합니다. 사용자의 개입 없이 사용하여 여러 태스크를 수행하고 정기적으로 자동으로 수행하도록 예약할 수 있는 runbook으로 이러한 프로세스를 구현할 수 있습니다.

## Runbook은 무엇입니까?

Runbook은 Azure 자동화에서 일부 자동화 된 프로세스를 수행하는 작업의 집합입니다. 예: 가상 컴퓨터를 시작 또는 로그 항목을 만드는 간단한 프로세스 중이거나 여러 리소스 또는 심지어 여러 클라우드에 걸쳐 복잡 한 프로세스를 수행하는 더 작은 다른 runbook을 결합하여 복잡한 runbook을 만들 수 있습니다.

예를 들어, 새 가상 컴퓨터를 프로비저닝의 기존의 수동 프로세스는 가상 컴퓨터 생성, 네트워크에 연결, IP 주소 할당, 사용할 준비가 되었음을 사용자에게 알림과 같은 여러 단계가 포함되었습니다. 이러한 각 단계를 수동으로 수행하는 대신 단일 프로세스로 이러한 작업을 모두 수행하는 runbook을 만들 수 있습니다. runbook을 실행하면, 가상 컴퓨터 이름, IP 주소, 이메일 수취인 등 요구하는 정보만 제공하고 프로세스를 완료하는 동안 가만히 앉아서 기다리기만 하면 됩니다.


## Runbook 자동화가 할 수 있는것은 무엇입니까?

Azure 자동화의 runbook은 Powershell 워크플로 기반이기 때문에 Powershell이 할 수 있는 것이라면 무엇이든 할 수 있습니다. API가 있는 응용 프로그램이거나 서비스라면 runbook과 함께 작업할 수 있습니다. 해당 Powershell 모듈이 있다면, 모듈을 Azure 자동화로 로드 및 runbook에 있는 cmdlets를 포함 할 수 있습니다. Azure 자동화 runbook은 Azure cloud에서 실행되기 때문에 클라우드의 모든 리소스 혹은 클라우드에서 액세스할 수 있는 모든 리소스에 액세스할 수 있습니다. [하이브리드 Runbook Worker](automation-hybrid-runbook-worker.md)를 사용하여, runbook을 로컬 데이터 센터에서 실행하여 로컬 리소스를 관리할 수 있습니다.


## Runbook는 어디서 얻을 수 있습니까?

[Runbook 갤러리](http://msdn.microsoft.com/library/azure/dn781422.aspx)는 Microsoft runbook 및 사용자 환경에서 그대로 사용하거나 용도에 맞게 사용자 지정한 커뮤니티 runbook을 포함합니다. 사용자 고유의 runbook을 만드는 방법에 학습에 대한 참조로 유용할 수도 있습니다. 다른사람들에게도 유용할 것 같은 사용자 고유의 runbook을 갤러리에 제공 할 수도 있습니다.


## 나만의 runbook을 만들려면 어떻게 해야 합니까?

처음부터 [고유한 runbook 만들기](http://msdn.microsoft.com/library/azure/dn643637.aspx)를 사용하거나 [Runbook 갤러리](http://msdn.microsoft.com/library/azure/dn781422.aspx)에서 사용자 요구 사항으로 runbook을 수정할 수 있습니다. PowerShell 코드로 직접 작업하는 것을 선호하는 경우, Azure 포털에서 [텍스트 편집기를 사용하여 runbook을 편집](http://msdn.microsoft.com/library/azure/dn879137.aspx)하거나 오프라인 편집합니다. 코드를 사용하지 않는 편집을 선호하는 경우, Azure Preview 포털에서 [그래픽 편집기](automation-graphical-authoring-intro.md)를 사용할 수 있습니다.


## Azure 자동화는 다른 자동화 도구와 어떤 관계가 있습니까?

[SMA (서비스 관리 자동화)](http://technet.microsoft.com/library/dn469260.aspx)는 사설 클라우드에서 관리 작업을 자동화하기 위해 고안되었습니다. [Windows Azure Pack](http://www.microsoft.com/server-cloud/products/windows-azure-pack/default.aspx)의 구성 요소로 데이터 센터에 로컬로 설치되었습니다. SMA와 Azure 자동화는 Windows PowerShell 워크플로를 기반으로 한 동일한 runbook을 사용하지만, SMA는 [그래픽 runbook](automation-graphical-authoring-intro.md)을 지원하지 않습니다.

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)은 온-프레미스 리소스의 자동화를 위한 것입니다. Azure 자동화 및 서비스 관리 자동화와 다른 runbook 형식을 사용하고 스크립팅 필요없이 runbook을 만드는 그래픽 인터페이스가 있습니다. 해당 runbook은 Ochestrator에 대해 구체적으로 적힌 통합 팩의 활동들로 구성됩니다.

## 자세한 정보는 어디서 얻을 수 있습니까?

다양한 리소스를 통해 Azure 자동화 및 고유한 Runbook 만들기에 대한 자세한 내용을 확인할 수 있습니다.

- **Azure 자동화 라이브러리**는 지금 현재 위치입니다. 이 문서에서는 Azure 자동화를 구성 및 관리하고 사용자 고유의 Runbook을 만드는 방법에 대한 전체 설명서를 제공합니다.
- [Azure PowerShell cmdlet](http://msdn.microsoft.com/library/jj156055.aspx)에서는 Windows PowerShell을 사용하여 Azure 작업을 자동화하는 방법에 대한 정보를 제공합니다. Runbook에서는 이러한 cmdlet을 사용하여 Azure 리소스로 작업합니다.
- [Azure 자동화 블로그](http://azure.microsoft.com/blog/tag/azure-automation)에서는 Microsoft에서 제공하는 Azure 자동화에 대한 최신 정보를 제공합니다. Azure 자동화 팀에서 제공하는 최신 정보를 받으려면 Azure 자동화 블로그를 구독하세요.
- [자동화 포럼](http://go.microsoft.com/fwlink/p/?LinkId=390561)에서는 Microsoft 및 자동화 커뮤니티에서 다루는 Azure 자동화에 대한 질문을 게시할 수 있습니다.

## 피드백을 제공할 수 있습니까?

**사용자 의견을 보내 주십시오!** Azure 자동화 Runbook 솔루션 또는 통합 모듈을 찾고 있는 경우 스크립트 센터에 스크립트 요청을 게시하세요. Azure 자동화에 대한 의견이나 기능 요청이 있는 경우 [사용자 음성](http://feedback.windowsazure.com/forums/34192--general-feedback)에 게시하세요. 감사합니다.

<!---HONumber=August15_HO6-->