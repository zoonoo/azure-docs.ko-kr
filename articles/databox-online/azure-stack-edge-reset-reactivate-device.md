---
title: Azure Stack Edge Pro 장치를 다시 설정 하 고 다시 활성화 | Microsoft Docs
description: 에서 데이터를 초기화 하 고 Azure Stack Edge Pro 장치를 다시 활성화 하는 방법을 알아봅니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/01/2020
ms.author: alkohli
ms.openlocfilehash: 4026bac9818b14c33c05d99caff4052adad196c3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745844"
---
# <a name="reset-and-reactivate-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro 장치를 다시 설정 하 고 다시 활성화 합니다.

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 장치에 문제가 있거나 다른 이유로 인해 새를 시작 해야 하는 경우 Azure Stack Edge Pro 장치를 다시 설정, 다시 구성 및 다시 활성화 하는 방법을 설명 합니다.

장치를 다시 설정 하 여 데이터를 제거한 후에는 새 리소스로 장치를 다시 활성화 해야 합니다. 장치를 다시 설정 하면 장치 구성이 제거 되므로 로컬 웹 UI를 통해 장치를 다시 구성 해야 합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * 디바이스의 데이터 디스크에서 데이터 지우기
> * 새 주문을 만들고 장치를 다시 구성 하 고 활성화 하 여 장치를 다시 활성화 합니다.

## <a name="reset-data-from-the-device"></a>장치에서 데이터 다시 설정

디바이스의 데이터 디스크에서 데이터를 지우려면 디바이스를 초기화해야 합니다. 

필요한 경우 초기화하기 전에 디바이스의 로컬 데이터를 복사합니다. 디바이스에서 Azure Storage 컨테이너로 데이터를 복사할 수 있습니다.

>[!IMPORTANT]
> 장치를 다시 설정 하면 장치에서 모든 로컬 데이터 및 작업을 삭제 하며 되돌릴 수 없습니다. 장치를 사용 하 여 새로를 시작 하려는 경우에만 장치를 다시 설정 합니다.

로컬 웹 UI 또는 PowerShell에서 장치를 다시 설정할 수 있습니다. PowerShell 지침은 [장치 다시 설정](./azure-stack-edge-connect-powershell-interface.md#reset-your-device)을 참조 하세요.

[! 포함] [장치에서 데이터 다시 설정](../../includes/azure-stack-edge-device-reset.md)

## <a name="reactivate-device"></a>장치 다시 활성화

장치를 다시 설정한 후에는 새 리소스로 장치를 다시 활성화 해야 합니다. 새 주문을 배치한 후 새 리소스를 다시 구성 하 고 다시 활성화 해야 합니다.

기존 장치를 다시 활성화 하려면 다음 단계를 수행 합니다.

1. [새 리소스 만들기](azure-stack-edge-gpu-deploy-prep?tabs=azure-portal#create-a-new-resource)의 단계에 따라 기존 장치에 대 한 새 주문을 만듭니다. **배송 주소** 탭에서 **이미 장치가** 있습니다 .를 선택 합니다.

   ![배송 주소에 새 장치를 지정 하지 않음](./media/azure-stack-edge-reset-reactivate-device/create-resource-with-no-new-device.png)

1. [활성화 키를 가져옵니다](azure-stack-edge-gpu-deploy-prep?tabs=azure-portal#get-the-activation-key).

1. [장치에 연결](azure-stack-edge-gpu-deploy-connect.md)합니다.

1. [장치에 대 한 네트워크를 구성](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)합니다.

1. [장치 설정을 구성](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)합니다.

1. [인증서를 구성](azure-stack-edge-gpu-deploy-configure-certificates.md)합니다.

1. [장치를 활성화](databox-online/azure-stack-edge-gpu-deploy-activate.md)합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro 장치에 연결](azure-stack-edge-gpu-deploy-connect.md)하는 방법을 알아봅니다.
