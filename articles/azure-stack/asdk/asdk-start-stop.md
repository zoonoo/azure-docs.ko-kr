---
title: 시작 및 중지 된 Azure Stack 개발 키트 ASDK () | Microsoft Docs
description: 시작 하 고 Azure Stack 개발 키트 (ASDK) 아래로 종료 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: e0b830a8c785ecab7e8f0e90cf9b9a702cbf25db
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250693"
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>시작 및 중지 된 Azure Stack 개발 키트 ASDK)
단순히 ASDK 호스트 컴퓨터를 다시 시작 하는 권장 되지 않습니다. 대신이 문서의 적절히 종료 하 고 ASDK 서비스를 다시 시작 절차 따라야 합니다. 

## <a name="stop-azure-stack"></a>Azure Stack 중지 
제대로 Azure Stack 서비스 및 ASDK 호스트 컴퓨터를 종료 하려면 다음 PowerShell 명령을 사용 합니다.

1. ASDK 호스트 컴퓨터의 AzureStack\CloudAdmin로 로그인 합니다.
2. (PowerShell ISE 없습니다 ()를 관리자 권한으로 PowerShell을 엽니다.
3. 권한 있는 끝점 (PEP) 세션을 설정 하려면 다음 명령을 실행 합니다. 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. 다음 PEP 세션에서 사용 하는 **중지 AzureStack** Azure Stack 서비스를 중지 하 고 ASDK 호스트 컴퓨터를 종료 하는 cmdlet:

   ```powershell
   Stop-AzureStack
   ```
5. 모든 Azure Stack 서비스를 성공적으로 종료 ASDK 호스트 컴퓨터를 종료 하기 전에 되도록 PowerShell 출력을 검토 합니다. 종료 프로세스는 몇 분 정도 걸립니다.

## <a name="start-azure-stack"></a>Azure Stack 시작 
ASDK 서비스는 호스트 컴퓨터를 시작할 때 자동으로 시작 해야 합니다. 그러나 ASDK 인프라 서비스 시작 시간 ASDK 호스트 컴퓨터의 하드웨어 구성이 성능에 따라 달라 집니다. 일부 경우에서 성공적으로 다시 시작 하는 모든 서비스에 대 한 몇 시간이 걸릴 수 있습니다.

ASDK 종료 된 방법에 관계 없이 모든 Azure Stack 서비스가 시작 되 고 완벽 하 게 작동 호스트 컴퓨터의 전원이 후 확인 하려면 다음 단계를 사용 해야 합니다. 

1. ASDK 호스트 컴퓨터의 전원을 켭니다. 
2. ASDK 호스트 컴퓨터의 AzureStack\CloudAdmin로 로그인 합니다.
3. (PowerShell ISE 없습니다 ()를 관리자 권한으로 PowerShell을 엽니다.
4. 권한 있는 끝점 (PEP) 세션을 설정 하려면 다음 명령을 실행 합니다.

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. 다음으로, PEP 세션에서 Azure Stack 서비스의 시작 상태를 확인 하려면 다음 명령을 실행 합니다.

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Azure Stack 서비스가 성공적으로 다시 시작 되었는지 확인 하려면 출력을 검토 합니다.

올바르게 종료 하 고 Azure Stack 서비스를 다시 시작 하는 권장 되는 절차에 대 한 자세한 내용은 참조 하세요 [Start 및 stop Azure Stack](../azure-stack-start-and-stop.md)합니다. 

## <a name="troubleshoot-startup-and-shutdown"></a>시작 및 종료 문제 해결 
Azure Stack 서비스 ASDK 호스트 컴퓨터의 전원 후 2 시간 내에서 성공적으로 시작 하지 않습니다 하는 경우 다음이 단계를 수행 합니다.

1. ASDK 호스트 컴퓨터의 AzureStack\CloudAdmin로 로그인 합니다.
2. (PowerShell ISE 없습니다 ()를 관리자 권한으로 PowerShell을 엽니다.
3. 권한 있는 끝점 (PEP) 세션을 설정 하려면 다음 명령을 실행 합니다.

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. 다음으로, PEP 세션에서 Azure Stack 서비스의 시작 상태를 확인 하려면 다음 명령을 실행 합니다.

   ```powershell
   Test-AzureStack
   ```
5. 출력을 검토 하 고 오류를 해결 합니다. 자세한 내용은 [Azure Stack의 유효성 검사 테스트를 실행할](../azure-stack-diagnostic-test.md)합니다.
6. 실행 하 여 PEP 세션 내에서 Azure Stack 서비스를 다시 시작 합니다 **시작 AzureStack** cmdlet:

   ```powershell
   Start-AzureStack
   ```

실행 하는 경우 **시작 AzureStack** 오류와 결과 방문를 [Azure Stack 지원 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack) 문제 해결 지원 ASDK를 가져오려고 합니다. 

## <a name="next-steps"></a>다음 단계 
Azure Stack의 진단 도구에 대 한 자세한 로깅 문제를 참조 하세요 [진단 도구를 Azure Stack](../azure-stack-diagnostics.md)합니다.
