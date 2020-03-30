---
title: Azure 랩 서비스를 위한 PowerShell 모듈 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Lab 서비스의 아티팩트를 관리하는 데 도움이 되는 PowerShell 모듈에 대한 정보를 제공합니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2019
ms.author: spelluru
ms.openlocfilehash: cc82355ee43f9fee4f9c2e1bb1bcc0481e4dcea3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73609399"
---
# <a name="azlabservices-powershell-module-preview"></a>Az.LabServices PowerShell 모듈(미리 보기)
Az.LabServices는 Azure Lab 서비스의 관리를 간소화하는 PowerShell 모듈입니다. 랩 계정, 랩, VM 및 이미지를 생성, 쿼리, 업데이트 및 삭제하는 컴포셔블 함수를 제공합니다. 이 모듈에 대한 자세한 내용은 [GitHub의 Az.LabServices 홈 페이지를](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)참조하십시오.

> [!NOTE]
> 이 모듈은 **미리 보기입니다.** 

## <a name="example-command"></a>명령 예제
다음은 PowerShell 명령을 사용하여 모든 랩에서 실행 중인 모든 VM을 중지하는 예제입니다.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>시작
1. 컴퓨터에 없는 경우 [Azure PowerShell을](https://docs.microsoft.com/powershell/azure/overview) 설치합니다. 
2. 컴퓨터에 [Az.LabServices.psm1을](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) 다운로드합니다.
3. 모듈 가져오기:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. 다음 명령을 실행하여 구독의 모든 랩을 나열합니다.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>다음 단계
[GitHub의 Az.LabServices 홈 페이지를](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)참조하십시오.
