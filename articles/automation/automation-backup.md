<properties 
   pageTitle="Azure 자동화 백업"
   description="자동화 계정을 삭제한 후 보존할 수 있도록 자동화 계정의 내용을 백업하는 방법에 대해 설명합니다."
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
   ms.date="04/13/2015"
   ms.author="bwren" />

# Azure 자동화 백업

Microsoft Azure에서 자동화 계정을 삭제하면 Runbook, 모듈, 설정, 작업, 자산 등 해당 계정의 모든 개체가 삭제됩니다. 계정을 삭제한 후에는 개체를 복구할 수 없습니다. 자동화 계정을 삭제하기 전에 다음 정보를 사용하여 자동화 계정의 내용을 백업할 수 있습니다.

## Runbook

Azure 관리 포털 또는 Windows PowerShell의 [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx)을 사용하여 Runbook을 스크립트 파일로 내보낼 수 있습니다. [Runbook 만들기 또는 가져오기](https://msdn.microsoft.com/library/dn643637.aspx)에 설명된 것처럼 다른 자동화 계정으로 이러한 스크립트 파일을 가져올 수 있습니다.


## 통합 모듈

Azure 자동화에서는 통합 모듈을 내보낼 수 없습니다. 자동화 계정 외부에서 사용할 수 있는지 확인해야 합니다.

## 자산

Azure 자동화에서는 [자산](https://msdn.microsoft.com/library/dn939988.aspx)을 내보낼 수 없습니다. Azure 관리 포털을 사용할 때 변수, 자격 증명, 인증서, 연결 및 일정에 대한 세부 정보를 기록해 두어야 합니다. 그런 다음 다른 자동화로 가져온 Runbook에서 사용하는 자산을 수동으로 만들어야 합니다.

[Azure cmdlet](https://msdn.microsoft.com/library/dn690262.aspx)을 사용하여 암호화되지 않은 자산의 세부 정보를 검색하고 나중에 참조하기 위해 저장하거나 다른 자동화 계정에서 동일한 자산을 만들 수 있습니다.

자격 증명의 암호 필드 또는 암호화된 변수 값은 cmdlet을 사용하여 검색할 수 없습니다. 이러한 값을 모르는 경우 [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) 및 [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) 활동을 사용하여 Runbook에서 검색할 수 있습니다.

Azure 자동화에서는 인증서를 내보낼 수 없습니다. Azure 외부에서 인증서를 사용할 수 있는지 확인해야 합니다.

## 관련된 문서

- [Runbook 만들기 또는 가져오기](https://msdn.microsoft.com/library/dn643637.aspx)
- [자동화 자산](https://msdn.microsoft.com/library/dn939988.aspx)
- [Azure cmdlet](https://msdn.microsoft.com/library/dn690262.aspx) 

<!---HONumber=July15_HO3-->