---
title: Azure Portal를 사용 하 여 GPU와 Azure Stack Edge Pro와 관련 된 활성화 오류 문제 해결 | Microsoft Docs
description: Edge Pro GPU 정품 인증 및 주요 자격 증명 모음 관련 문제를 Azure Stack 문제를 해결 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.author: alkohli
ms.openlocfilehash: 33254c170c309626ecfa9099bc4d86578148f4c1
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91941391"
---
# <a name="troubleshoot-activation-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 장치의 정품 인증 문제 해결 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

이 문서에서는 Azure Stack Edge Pro GPU 장치에서 활성화 문제를 해결 하는 방법을 설명 합니다. 


## <a name="activation-errors"></a>활성화 오류

다음 표에는 장치 활성화와 관련 된 오류 및 해당 권장 해결 방법이 요약 되어 있습니다.

| 오류 메시지| 권장 해결 방법 |
|------------------------------------------------------|--------------------------------------|
| 활성화 키를 사용 하 여 장치를 활성화 하기 전에 활성화에 사용 된 Azure Key Vault를 삭제 하면이 오류가 표시 됩니다. <br> ![Key vault 오류 1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | 키 자격 증명 모음이 삭제 된 경우 자격 증명 모음이 제거 방지 기간에 있는 경우 key vault를 복구할 수 있습니다. [키 자격 증명 모음 복구](/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault)의 단계를 따릅니다. <br>제거 방지 기간이 경과 된 경우 key vault를 복구할 수 없습니다. Microsoft 지원에 다음 단계를 문의합니다. |
| 장치가 활성화 된 후에 Azure Key Vault 삭제 된 후 암호화를 포함 하는 작업 (예: **사용자 추가**, **공유 추가**, **계산 구성**)을 수행 하려고 하면이 오류가 표시 됩니다. <br> ![키 자격 증명 모음 오류 2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | 키 자격 증명 모음이 삭제 된 경우 자격 증명 모음이 제거 방지 기간에 있는 경우 key vault를 복구할 수 있습니다. 키 자격 증명 모음 복구의 단계를 따릅니다. <br>제거 방지 기간이 경과 된 경우 key vault를 복구할 수 없습니다. Microsoft 지원에 다음 단계를 문의합니다. |
| Azure Key Vault의 채널 무결성 키가 삭제 된 경우 **사용자 추가**, **공유 추가**, **계산 구성** 등 암호화와 관련 된 작업을 수행 하려고 시도 합니다. 그러면이 오류가 표시 됩니다. <br> ![Key vault 오류 3](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-3.png) | 키 자격 증명 모음에서 채널 무결성 키가 삭제 되었지만 아직 제거 기간 내에 있는 경우 [key vault 키 제거 실행 취소](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)의 단계를 따릅니다. <br>보호 제거 기간이 경과 하 고 키를 백업한 경우 백업에서 복원할 수 있습니다. 그렇지 않으면 키를 복구할 수 없습니다. Microsoft 지원에 다음 단계를 문의합니다. |
| 오류로 인해 활성화 키 생성이 실패 하면이 오류가 표시 됩니다. 추가 세부 정보가 알림에 표시 됩니다. <br> ![주요 자격 증명 모음 오류 4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | 몇 분 정도 기다린 후 작업을 다시 시도 하세요. 문제가 지속되면 Microsoft 지원에 문의하세요. |
| 사용자에 게 읽기 전용 권한이 있는 경우에는 사용자가 활성화 키를 생성할 수 없으며이 오류가 표시 됩니다. <br> ![주요 자격 증명 모음 오류 5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | 올바른 액세스 권한이 없거나  *Microsoft. KeyVault* 가 등록 되지 않았기 때문일 수 있습니다.<li>Azure Stack Edge 리소스에 사용 되는 리소스 그룹 수준에서 소유자 또는 참가자 액세스 권한이 있는지 확인 합니다.</li><li>Microsoft. KeyVault 리소스 공급자가 등록 되어 있는지 확인 합니다. 리소스 공급자를 등록 하려면 Azure Stack Edge 리소스에 사용 되는 구독으로 이동 합니다. **리소스 공급자**로 이동 하 고, *Microsoft keyvault* 를 검색 하 고,을 선택 하 고 **등록**합니다.</li> |

## <a name="next-steps"></a>다음 단계

- [장치 문제를 해결](azure-stack-edge-gpu-troubleshoot.md)하는 방법에 대해 자세히 알아보세요.
