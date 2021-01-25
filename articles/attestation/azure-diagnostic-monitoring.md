---
title: Azure 진단 모니터링 - Azure Attestation
description: Azure Attestation에 대한 Azure 진단 모니터링
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 40bc76f839cf6757b8f874112504249e611c3e1a
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98605948"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Azure Attestation의 TPM(신뢰할 수 있는 플랫폼 모듈) 엔드포인트를 사용하여 진단 설정

Azure 활동 로그 및 리소스 로그를 포함한 Azure의 [플랫폼 로그](/azure/azure-monitor/platform/platform-logs-overview)에서 Azure 리소스 및 이에 따른 Azure 플랫폼에 대한 자세한 진단 및 감사 정보를 제공합니다. [플랫폼 메트릭](/azure/azure-monitor/platform/data-platform-metrics)은 기본적으로 수집되며 일반적으로 Azure 모니터 메트릭 데이터베이스에 저장됩니다. 이 문서에서는 플랫폼 메트릭 및 플랫폼 로그를 다른 대상으로 보내기 위한 진단 설정을 만들고 구성하는 방법에 대한 세부 정보를 제공합니다. 

TPM 엔드포인트 서비스는 진단 설정으로 사용하도록 설정되며 작업을 모니터링하는 데 사용할 수 있습니다. PowerShell을 사용하여 TPM 서비스 엔드포인트에 대한 [Azure Monitoring](/azure/azure-monitor/overview)을 설정하려면 다음 단계를 수행합니다. 

Azure Attestation 서비스를 설정합니다. 

[Azure PowerShell을 사용하여 Azure Attestation 설정](/azure/attestation/quickstart-powershell#:~:text=%20Quickstart%3A%20Set%20up%20Azure%20Attestation%20with%20Azure,Register%20Microsoft.Attestation%20resource%20provider.%20Register%20the...%20More%20)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
활동 로그는 스토리지 계정의 컨테이너 섹션에서 찾을 수 있습니다. 자세한 정보는 [Azure 리소스에서 리소스 로그를 수집하고 Azure Monitor를 사용하여 분석 - Azure Monitor](/azure/azure-monitor/learn/tutorial-resource-logs)에서 찾을 수 있습니다.
