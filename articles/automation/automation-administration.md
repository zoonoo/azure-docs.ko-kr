<properties 
   pageTitle="Azure 자동화 관리"
   description="이 문서는 Azure 자동화 환경 관리에 대한 여러 항목을 포함합니다. 현재 데이터 보존 및 Azure 자동화 백업을 포함합니다."
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
   ms.date="07/06/2015"
   ms.author="bwren" />

# Azure 자동화 관리

이 문서는 Azure 자동화 환경 관리에 대한 여러 항목을 포함합니다.

## 데이터 보존

Azure 자동화에서 리소스를 삭제할 경우 영구적으로 제거하기 전에 감사를 위해 90일간 보존됩니다. 이 기간 동안에는 리소스를 보거나 사용할 수 없습니다. 이 정책은 삭제되는 자동화 계정에 속한 리소스에도 적용됩니다.

Azure 자동화는 90일이 넘은 작업을 자동으로 삭제하고 영구적으로 제거합니다.

다음 표에는 여러 리소스에 대한 보존 정책이 요약되어 있습니다.

|Data|정책|
|:---|:---|
|계정|사용자가 계정을 삭제한 후 90일이 지나면 영구적으로 제거됩니다.|
|자산|사용자가 자산을 삭제한 후 90일이 지나거나 사용자가 자산을 유지하는 계정을 삭제한 후 90일이 지나면 영구적으로 제거됩니다.|
|모듈|사용자가 모듈을 삭제한 후 90일이 지나거나 사용자가 모듈을 유지하는 계정을 삭제한 후 90일이 지나면 영구적으로 제거됩니다.|
|Runbook|사용자가 리소스를 제거한 후 90일이 지나거나 사용자가 리소스를 유지하는 계정을 삭제한 후 90일이 지나면 영구적으로 제거됩니다.|
|작업|마지막으로 수정한지 90일이 지나면 삭제 및 영구 제거됩니다. 이는 작업이 완료되거나 중지되거나 일시 중단된 이후일 수 있습니다.|

보존 정책은 모든 사용자에게 적용되며 지금은 사용자 지정할 수 없습니다.

## Azure 자동화 백업

Microsoft Azure에서 자동화 계정을 삭제하면 Runbook, 모듈, 설정, 작업, 자산 등 해당 계정의 모든 개체가 삭제됩니다. 계정을 삭제한 후에는 개체를 복구할 수 없습니다. 자동화 계정을 삭제하기 전에 다음 정보를 사용하여 자동화 계정의 내용을 백업할 수 있습니다.

### Runbook

Azure 관리 포털 또는 Windows PowerShell의 [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx)을 사용하여 Runbook을 스크립트 파일로 내보낼 수 있습니다. [Runbook 만들기 또는 가져오기](https://msdn.microsoft.com/library/dn643637.aspx)에 설명된 것처럼 다른 자동화 계정으로 이러한 스크립트 파일을 가져올 수 있습니다.


### 통합 모듈

Azure 자동화에서는 통합 모듈을 내보낼 수 없습니다. 자동화 계정 외부에서 사용할 수 있는지 확인해야 합니다.

### 자산

Azure 자동화에서는 [자산](https://msdn.microsoft.com/library/dn939988.aspx)을 내보낼 수 없습니다. Azure 관리 포털을 사용할 때 변수, 자격 증명, 인증서, 연결 및 일정에 대한 세부 정보를 기록해 두어야 합니다. 그런 다음 다른 자동화로 가져온 Runbook에서 사용하는 자산을 수동으로 만들어야 합니다.

[Azure cmdlet](https://msdn.microsoft.com/library/dn690262.aspx)을 사용하여 암호화되지 않은 자산의 세부 정보를 검색하고 나중에 참조하기 위해 저장하거나 다른 자동화 계정에서 동일한 자산을 만들 수 있습니다.

자격 증명의 암호 필드 또는 암호화된 변수 값은 cmdlet을 사용하여 검색할 수 없습니다. 이러한 값을 모르는 경우 [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) 및 [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) 활동을 사용하여 Runbook에서 검색할 수 있습니다.

Azure 자동화에서는 인증서를 내보낼 수 없습니다. Azure 외부에서 인증서를 사용할 수 있는지 확인해야 합니다.

<!---HONumber=August15_HO6-->