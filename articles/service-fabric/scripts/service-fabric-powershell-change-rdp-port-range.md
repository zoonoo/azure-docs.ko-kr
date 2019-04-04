---
title: Azure PowerShell 스크립트 샘플 - RDP 포트 범위 변경 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - 배포된 클러스터의 RDP 포트 범위를 변경합니다.
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
ms.date: 03/19/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 871fe502a40b4c22f3e57a2f357e940c7f659314
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663930"
---
# <a name="update-the-rdp-port-range-values"></a>RDP 포트 범위 값 업데이트

이 샘플 스크립트는 클러스터가 배포된 후 클러스터 노드 VM에서 RDP 포트 범위 값을 변경합니다.  Azure PowerShell이 사용되므로 기본 VM을 끄지 않습니다.  이 스크립트는 클러스터 리소스 그룹의 `Microsoft.Network/loadBalancers` 리소스를 가져오고 `inboundNatPools.frontendPortRangeStart` 및 `inboundNatPools.frontendPortRangeEnd` 값을 업데이트합니다. 필요에 따라 매개 변수를 사용자 지정합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/overview)에 있는 지침을 사용하여 Azure PowerShell을 설치합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | `Microsoft.Network/loadBalancers` 리소스를 가져옵니다. |
|[집합 AzResource](/powershell/module/az.resources/set-azresource)|`Microsoft.Network/loadBalancers` 리소스를 업데이트합니다.|

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

Azure Service Fabric에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../service-fabric-powershell-samples.md)에서 확인할 수 있습니다.
