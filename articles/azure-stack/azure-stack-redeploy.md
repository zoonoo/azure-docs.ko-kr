---
title: "Azure 스택 다시 배포 | Microsoft Docs"
description: "Azure 스택 다시 배포 합니다."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 891cde9b16bbbb51729129b6ad7a0f3794307baa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="redeploy-azure-stack"></a>Azure 스택 다시 배포
Azure 스택을 다시 배포 하려면 다시 시작 해야 처음부터 아래에서 설명 합니다.

## <a name="steps-to-redeploy-azure-stack"></a>Azure 스택 다시 배포 하는 단계
1. 개발 키트 호스트에서 관리자 권한 PowerShell 콘솔을 열고 > asdk installer.ps1 스크립트로 이동 > 실행 > 클릭 **다시 부팅**합니다.
2. 기본 운영 체제를 선택 (하지 **Azure 스택**)를 클릭 하 고 **다음**합니다.
3. 개발 키트 호스트 다시 부팅 한 후 이전 배포의 일환으로 사용한 CloudBuilder.vhdx 파일을 삭제 합니다.
4. [개발 키트 배포](azure-stack-run-powershell-script.md)합니다.

## <a name="next-steps"></a>다음 단계
[Azure Stack에 연결](azure-stack-connect-azure-stack.md)

