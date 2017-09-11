---
title: "Azure PowerShell 스크립트 샘플 - 클러스터에서 응용 프로그램 제거 | Microsoft Docs"
description: "Azure PowerShell 스크립트 샘플 - Service Fabric 클러스터에서 응용 프로그램 제거"
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
ms.openlocfilehash: 951c9a014f587ce53450dd705fc478da625b1e6b
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Service Fabric 클러스터에서 응용 프로그램 제거

이 샘플 스크립트는 실행 중인 Service Fabric 응용 프로그램 인스턴스를 삭제하고, 클러스터에서 응용 프로그램 유형 및 버전을 등록 취소하고, 클러스터 이미지 저장소에서 응용 프로그램 패키지를 삭제합니다.  응용 프로그램 인스턴스를 삭제하면 해당 응용 프로그램과 연결된 실행 중인 모든 서비스 인스턴스도 삭제됩니다. 필요에 따라 매개 변수를 사용자 지정합니다. 

필요한 경우 [Service Fabric SDK](../service-fabric-get-started.md)를 사용하여 Service Fabric PowerShell 모듈을 설치합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "클러스터에서 응용 프로그램 제거")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | 클러스터에서 실행 중인 Service Fabric 응용 프로그램 인스턴스를 제거합니다.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | 클러스터에서 Service Fabric 응용 프로그램 유형 및 버전을 등록 취소합니다. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | 이미지 저장소에서 Service Fabric 응용 프로그램 패키지를 제거합니다.|

## <a name="next-steps"></a>다음 단계

Service Fabric PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/service-fabric/?view=azureservicefabricps)를 참조하세요.

Azure Service Fabric에 대한 추가 PowerShell 샘플은 [Azure PowerShell 샘플](../service-fabric-powershell-samples.md)에서 확인할 수 있습니다.

