---
title: Azure PowerShell 스크립트 샘플 - 네트워크 보안 그룹 규칙 추가 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - 특정 포트에서 인바운드 트래픽을 허용하는 네트워크 보안 그룹을 추가합니다.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 11/28/2017
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 92efd6c4e63959b6098829892c5cbbaeeabe9f83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66159993"
---
# <a name="add-an-inbound-network-security-group-rule"></a>인바운드 네트워크 보안 그룹 규칙 추가

이 샘플 스크립트는 포트 8081에서 인바운드 트래픽을 허용하도록 네트워크 보안 그룹 규칙을 만듭니다.  이 스크립트는 클러스터가 위치한 `Microsoft.Network/networkSecurityGroups` 리소스를 가져오고, 새 네트워크 보안 구성 규칙을 만들고, 네트워크 보안 그룹을 업데이트합니다. 필요에 따라 매개 변수를 사용자 지정합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/overview)에 있는 지침을 사용하여 Azure PowerShell을 설치합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | `Microsoft.Network/networkSecurityGroups` 리소스를 가져옵니다. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| 이름으로 네트워크 보안 그룹을 가져옵니다.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 네트워크 보안 그룹에 네트워크 보안 규칙 구성을 추가합니다. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| 네트워크 보안 그룹에 대한 목표 상태를 설정합니다.|

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.
