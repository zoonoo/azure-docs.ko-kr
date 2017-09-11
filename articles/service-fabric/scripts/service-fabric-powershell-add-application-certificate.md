---
title: "Azure PowerShell 스크립트 샘플 - 클러스터에 응용 프로그램 인증서 추가 | Microsoft Docs"
description: "Azure PowerShell 스크립트 샘플 - Service Fabric 클러스터에 응용 프로그램 인증서 추가"
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 06/20/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 3212944a8bd882ced86e841944ac60bd1845e3d1
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Service Fabric 클러스터에 응용 프로그램 인증서 추가

이 샘플 스크립트는 지정된 Azure Key Vault에서 자체 서명된 인증서를 만들고 Service Fabric 클러스터의 모든 노드에 설치합니다. 또한 인증서는 로컬 폴더로도 다운로드됩니다. 다운로드한 인증서의 이름은 Key Vault의 인증서 이름과 동일합니다. 필요에 따라 매개 변수를 사용자 지정합니다.

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/overview)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Login-AzureRmAccount`를 실행하여 Azure에 연결합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-application-certificate/add-new-application-certificate.ps1 "클러스터에 응용 프로그램 인증서 추가")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 표의 각 명령은 명령 관련 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [Add-AzureRmServiceFabricApplicationCertificate](/powershell/module/azurerm.servicefabric/Add-AzureRmServiceFabricApplicationCertificate) | 클러스터를 구성하는 가상 컴퓨터 확장 집합에 새 응용 프로그램 인증서를 추가합니다.  |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

Azure Service Fabric에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../service-fabric-powershell-samples.md)에서 확인할 수 있습니다.

