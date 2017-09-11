---
title: "Azure PowerShell 스크립트 샘플 - 클러스터에 응용 프로그램 배포 | Microsoft Docs"
description: "Azure PowerShell 스크립트 샘플 - 클러스터에 응용 프로그램 배포"
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
ms.openlocfilehash: b9aa30e18f6997050f29792e53946d5ec747e9ff
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Service Fabric 클러스터에 응용 프로그램 배포

이 샘플 스크립트는 클러스터 이미지 저장소에 응용 프로그램 패키지를 복사하고, 응용 프로그램 유형을 클러스터에 등록하고, 해당 응용 프로그램 유형에서 응용 프로그램 인스턴스를 만듭니다.  기본 서비스가 대상 응용 프로그램 형식의 응용 프로그램 매니페스트에 정의된 경우 이때 이러한 서비스도 생성됩니다. 필요에 따라 매개 변수를 사용자 지정합니다. 

필요한 경우 [Service Fabric SDK](../service-fabric-get-started.md)를 사용하여 Service Fabric PowerShell 모듈을 설치합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "클러스터에 응용 프로그램 배포")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플이 실행된 후에 [응용 프로그램 제거](service-fabric-powershell-remove-application.md)의 스크립트를 사용하여 응용 프로그램 인스턴스를 제거하고, 응용 프로그램 유형을 등록 취소하고, 이미지 저장소에서 응용 프로그램 패키지를 삭제할 수 있습니다.

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | 응용 프로그램 패키지를 클러스터 이미지 저장소에 복사합니다.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| 응용 프로그램 유형 및 버전을 클러스터에 등록합니다. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| 등록된 응용 프로그램 유형에서 응용 프로그램을 만듭니다. |

## <a name="next-steps"></a>다음 단계

Service Fabric PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/service-fabric/?view=azureservicefabricps)를 참조하세요.

Azure Service Fabric에 대한 추가 PowerShell 샘플은 [Azure PowerShell 샘플](../service-fabric-powershell-samples.md)에서 확인할 수 있습니다.

