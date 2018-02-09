---
title: "Azure 스택 다시 배포 | Microsoft Docs"
description: "Azure 스택 다시 배포 합니다."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 0dec5ea70376ff1c8cf488689f1a66190256f6ff
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2018
---
# <a name="redeploy-azure-stack"></a>Azure 스택 다시 배포
Azure 스택을 배포 하는 동안 오류가 나타나면 다음 PowerShell 명령을 사용 하 여 설치 다시 실행할 수 있습니다: `.\InstallAzureStackpoc.ps1 -rerun`합니다. 이 명령은 Azure 스택 설치 프로그램을 처음부터 다시 시작 하지 않고도 이전에 실패 한 지점에서 다시 시작 됩니다. 동일한 설치 오류를 다시 표시 되 면 전체적 주소 문제를 수행 해야 할 수 있습니다. 

Azure 스택을 다시 배포 하려면 다시 시작 해야 처음부터 아래에서 설명 합니다.

## <a name="steps-to-redeploy-azure-stack"></a>Azure 스택 다시 배포 하는 단계
1. 개발 키트 호스트에서 관리자 권한 PowerShell 콘솔을 열고 > asdk installer.ps1 스크립트로 이동 > 실행 > 클릭 **다시 부팅**합니다.
2. 기본 운영 체제를 선택 (하지 **Azure 스택**)를 클릭 하 고 **다음**합니다.
3. 개발 키트 호스트 다시 부팅 한 후 이전 배포의 일환으로 사용한 CloudBuilder.vhdx 파일을 삭제 합니다.
4. [개발 키트 배포](azure-stack-run-powershell-script.md)합니다.

## <a name="next-steps"></a>다음 단계
[Azure Stack에 연결](azure-stack-connect-azure-stack.md)

