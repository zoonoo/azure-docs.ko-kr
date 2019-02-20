---
title: 시작 하 고 Azure Stack 중지 | Microsoft Docs
description: 시작 하 고 Azure Stack을 종료 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 6b14f5e8967567030ce854d05c53a8d26027e9a6
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428842"
---
# <a name="start-and-stop-azure-stack"></a>시작 하 고 Azure Stack 중지
이 문서의 적절히 종료 하 고 Azure Stack 서비스를 다시 시작 절차를 따라야 합니다. 종료는 실제로 환경의 전원을 끄고 전체 Azure Stack. 모든 인프라 역할의 전원을 켭니다 시작 되 고 반환 리소스 종료 전에 있었을 전원 상태를 테 넌 트입니다.

## <a name="stop-azure-stack"></a>Azure Stack 중지 

다음 단계를 사용 하 여 Azure Stack를 종료 합니다.

1. 예정 된 종료에 대 한 Azure Stack 환경과 테 넌 트 리소스에서 실행 되는 모든 워크 로드를 준비 합니다. 

2. Azure Stack ERCS Vm에 대 한 네트워크 액세스를 사용 하 여 컴퓨터에서 권한 있는 끝점 세션 (PEP)를 엽니다. 자세한 내용은 [권한 있는 끝점을 사용 하 여 Azure Stack에서](azure-stack-privileged-endpoint.md)합니다.

3. PEP에서 다음을 실행 합니다.

    ```powershell
      Stop-AzureStack
    ```

4. 전원에 실제 Azure Stack 노드를 모두 해제 기다립니다.

> [!Note]  
> 원래 장비 제조업체 (OEM) Azure Stack 하드웨어를 제공 하는 지침에 따라 실제 노드의 전원 상태를 확인할 수 있습니다. 

## <a name="start-azure-stack"></a>Azure Stack 시작 

다음 단계를 사용 하 여 Azure Stack을 시작 합니다. Azure Stack 중지 하는 방법에 관계 없이 다음이 단계를 수행 합니다.

1. 각 Azure Stack 환경에서 실제 노드의 전원을 켭니다. 원래 장비 제조업체 (OEM) Azure Stack에 대 한 하드웨어를 제공 하는 지침에 따라 실제 노드에 대 한 지침에 기능을 확인 합니다.

2. Azure Stack 인프라 서비스가 시작 될 때까지 기다립니다. Azure Stack 인프라 서비스 시작 프로세스를 완료 하는 데 두 시간이 필요할 수 있습니다. 사용 하 여 Azure Stack의 시작 상태를 확인할 수 있습니다 합니다 [ **Get ActionStatus** cmdlet](#get-the-startup-status-for-azure-stack)합니다.

3. 종료 하기 전에 상태로를 반환한 모든 테 넌 트 리소스를 확인 합니다. 테 넌 트 리소스에서 실행 중인 워크 로드는 워크 로드 관리자에 의해 시작 된 후 다시 구성 해야 합니다.

## <a name="get-the-startup-status-for-azure-stack"></a>Azure Stack에 대 한 시작 상태 가져오기

다음 단계를 사용 하 여 Azure Stack 시작 루틴에 대 한 시작을 가져옵니다.

1. Azure Stack ERCS Vm에 대 한 네트워크 액세스를 사용 하 여 컴퓨터에서 권한 있는 끝점 세션을 엽니다.

2. PEP에서 다음을 실행 합니다.

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Azure Stack의 시작 및 종료 문제 해결

인프라 및 테 넌 트 서비스를 Azure Stack 환경에서 전원 이후 2 시간을 성공적으로 시작 하지 않습니다 하는 경우 다음 단계를 수행 합니다. 

1. Azure Stack ERCS Vm에 대 한 네트워크 액세스를 사용 하 여 컴퓨터에서 권한 있는 끝점 세션을 엽니다.

2. 다음을 실행합니다. 

    ```powershell
      Test-AzureStack
      ```

3. 출력을 검토 하 고 상태 오류를 해결 합니다. 자세한 내용은 [Azure Stack의 유효성 검사 테스트를 실행할](azure-stack-diagnostic-test.md)합니다.

4. 다음을 실행합니다.

    ```powershell
      Start-AzureStack
    ```

5. 실행 하는 경우 **시작 AzureStack** 결과 오류와 Microsoft 고객 서비스 지원에 문의 합니다. 

## <a name="next-steps"></a>다음 단계 

자세한 내용은 [Azure Stack의 진단 도구](azure-stack-diagnostics.md)
