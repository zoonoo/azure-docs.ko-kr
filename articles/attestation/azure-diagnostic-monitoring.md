---
title: Azure 진단 모니터링 - Azure Attestation
description: Azure Attestation에 대한 Azure 진단 모니터링
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d01e7817906927295591353b710afe2899aacdf1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726481"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Azure Attestation의 TPM(신뢰할 수 있는 플랫폼 모듈) 엔드포인트를 사용하여 진단 설정

Azure 활동 로그 및 리소스 로그를 포함한 Azure의 [플랫폼 로그](../azure-monitor/essentials/platform-logs-overview.md)에서 Azure 리소스 및 이에 따른 Azure 플랫폼에 대한 자세한 진단 및 감사 정보를 제공합니다. [플랫폼 메트릭](../azure-monitor/essentials/data-platform-metrics.md)은 기본적으로 수집되며 일반적으로 Azure 모니터 메트릭 데이터베이스에 저장됩니다. 이 문서에서는 플랫폼 메트릭 및 플랫폼 로그를 다른 대상으로 보내기 위한 진단 설정을 만들고 구성하는 방법에 대한 세부 정보를 제공합니다. 

TPM 엔드포인트 서비스는 진단 설정으로 사용하도록 설정되며 작업을 모니터링하는 데 사용할 수 있습니다. PowerShell을 사용하여 TPM 서비스 엔드포인트에 대한 [Azure Monitoring](../azure-monitor/overview.md)을 설정하려면 다음 단계를 수행합니다. 

Azure Attestation 서비스를 설정합니다. 

[Azure PowerShell을 사용하여 Azure Attestation 설정](./quickstart-powershell.md)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
활동 로그는 스토리지 계정의 컨테이너 섹션에서 찾을 수 있습니다. 자세한 정보는 [Azure 리소스에서 리소스 로그를 수집하고 Azure Monitor를 사용하여 분석 - Azure Monitor](../azure-monitor/essentials/tutorial-resource-logs.md)에서 찾을 수 있습니다.
