---
title: Azure PowerShell 스크립트 샘플 - 클러스터에 애플리케이션 인증서 추가 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - Service Fabric 클러스터에 애플리케이션 인증서 추가
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
ms.date: 01/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 313a1b7e4f0109a0a5cb94214918d97c25b5cf5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66153839"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Service Fabric 클러스터에 애플리케이션 인증서 추가

이 샘플 스크립트는 지정된 Azure Key Vault에서 자체 서명된 인증서를 만들고 Service Fabric 클러스터의 모든 노드에 설치합니다. 또한 인증서는 로컬 폴더로도 다운로드됩니다. 다운로드한 인증서의 이름은 Key Vault의 인증서 이름과 동일합니다. 필요에 따라 매개 변수를 사용자 지정합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/overview)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Connect-AzAccount`를 실행하여 Azure에 연결합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-application-certificate/add-new-application-certificate.ps1 "Add an application certificate to a cluster")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Add-AzServiceFabricApplicationCertificate](/powershell/module/az.servicefabric/Add-azServiceFabricApplicationCertificate) | 클러스터를 구성하는 가상 머신 확장 세트에 새 애플리케이션 인증서를 추가합니다.  |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

Azure Service Fabric에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../service-fabric-powershell-samples.md)에서 확인할 수 있습니다.
