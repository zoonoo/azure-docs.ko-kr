<properties 
   pageTitle="Runbook 설정"
   description="Azure 자동화의 Runbook에 대한 구성 설정 및 Azure 관리 포털과 Windows PowerShell을 사용하여 이를 변경하는 방법에 대해 설명합니다."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/22/2015"
   ms.author="bwren" />

# Runbook 설정

Azure 자동화의 각 Runbook에는 해당 로깅 동작을 쉽게 식별하고 변경하는 데 유용한 여러 설정이 있습니다. 이러한 각 설정은 아래에서 해당 설정을 수정하는 방법에 대한 절차 다음에 설명되어 있습니다.

## 설정

### 이름 및 설명

Runbook을 만든 후에는 이름을 변경할 수 없습니다. 이 설명은 선택 사항이며, 최대 512자일 수 있습니다.

### 태그

태그를 사용하면 Runbook을 쉽게 식별할 수 있는 고유한 단어 및 구를 할당할 수 있습니다. 예를 들어 [Runbook 갤러리](https://msdn.microsoft.com/library/dn781422.aspx)에 Runbook을 제출할 때 Runbook이 나열되어야 하는 범주를 식별하는 특정 태그를 지정합니다. 쉼표로 구분하여 Runbook에 대한 여러 태그를 지정할 수 있습니다.

### 로깅

기본적으로 자세한 정보 표시 및 진행률 레코드 작업 기록에 기록되지 않습니다. 이러한 레코드를 기록하려면 특정 Runbook에 대한 설정을 변경하면 됩니다. 이러한 레코드에 대한 자세한 내용은 [Runbook 출력 및 메시지](https://msdn.microsoft.com/library/dn879148.aspx)를 참조하세요.

## Runbook 설정 변경

### Azure 관리 포털을 사용하여 Runbook 설정 변경

Azure 관리 포털의 Runbook에 대한 **구성** 페이지에서 해당 Runbook의 설정을 변경할 수 있습니다.

1. Azure 관리 포털에서 **자동화**를 선택한 다음 자동화 계정의 이름을 클릭합니다.
1. **Runbook** 탭을 선택합니다.
1. Runbook의 이름을 클릭합니다.
1. **구성** 탭을 선택합니다.

### Windows PowerShell을 사용하여 Runbook 설정 변경

[Set-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690275.aspx) cmdlet을 사용하여 Runbook의 설정을 변경할 수 있습니다. 여러 태그를 지정하려면 Tags 매개 변수에 배열 또는 쉼표로 구분된 값이 있는 단일 문자열을 제공합니다. [Get-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690278.aspx)을 사용하여 현재 태그를 가져올 수 있습니다.

다음 명령 예제에서는 Rnbook에 대한 속성을 설정하는 방법을 보여 줍니다. 이 예제에서는 기존 태그에 세 개의 태그를 추가하고 자세한 정보 표시 레코드가 기록되도록 지정합니다.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Sample-TestRunbook"
	$tags = (Get-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName).Tags
	$tags += "Tag1,Tag2,Tag3"
	Set-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## 관련된 문서
- [Runbook 출력 및 메시지](../automation-runbook-output-and-messages) 
- [Runbook 만들기 또는 가져오기](https://msdn.microsoft.com/library/dn643637.aspx) 

<!---HONumber=Nov15_HO1-->