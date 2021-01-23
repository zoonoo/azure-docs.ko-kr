---
title: Azure Stack Edge Pro 장치 교체 | Microsoft Docs
description: 대체 Azure Stack Edge Pro 장치를 가져오는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 91cb446da31f353162f90778d855056a9697d455
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726578"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro 장치 교체

이 문서에서는 Azure Stack Edge Pro 장치를 교체 하는 방법을 설명 합니다. 기존 디바이스에 하드웨어 오류가 있거나 업그레이드가 필요한 경우 교체 디바이스가 필요합니다. 


이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * 하드웨어 문제에 대 한 지원 티켓 열기
> * Azure Portal에서 교체 장치의 새 주문 만들기
> * 교체 장치를 설치 하 고 활성화 합니다.
> * 원래 장치 반환

## <a name="open-a-support-ticket"></a>지원 티켓 열기

기존 장치에 하드웨어 오류가 있는 경우 다음 단계를 수행 하 여 지원 티켓을 엽니다.

1. Microsoft 지원에서 지원 티켓을 열어 디바이스 반환 의사를 밝힙니다. **Azure Stack Edge Pro 하드웨어** 문제 유형을 선택 하 고 **하드웨어 문제** 하위 유형을 선택 합니다.  

    ![지원 티켓 열기](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Microsoft 지원 엔지니어는 FRU (필드 교체 단위)가 문제를 해결할 수 있고이 인스턴스에 사용할 수 있는지를 확인 하는 데 도움을 받을 수 있습니다. FRU를 사용할 수 없거나 장치에 하드웨어 업그레이드가 필요한 경우에는 새 주문을 입력 하 고 이전 장치를 반환 하는 방법을 안내 합니다.

## <a name="create-a-new-order"></a>새 주문 만들기

[새 리소스 만들기](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)의 단계를 수행 하 여 교체 장치를 활성화 하는 데 사용할 새 리소스를 만듭니다.

> [!NOTE]
> 기존 리소스에 대해 교체 장치를 활성화 하는 것은 지원 되지 않습니다. 새 리소스는 새 순서로 간주 됩니다. 장치를 배송 한 후 14 일 후에 요금을 청구 하기 시작 합니다.

## <a name="install-and-activate-the-replacement-device"></a>교체 장치 설치 및 활성화

교체 장치를 설치 하 고 활성화 하려면 다음 단계를 수행 합니다.

1. [장치를 설치](azure-stack-edge-deploy-install.md)합니다.
2. 이전에 만든 새 리소스에 대해 [장치를 활성화](azure-stack-edge-deploy-connect-setup-activate.md) 합니다.

## <a name="return-your-existing-device"></a>기존 장치 반환

모든 단계에 따라 원래 디바이스를 반환합니다.

1. [디바이스에서 데이터를 지웁니다](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. 원본 장치에 대 한 [장치 반환을 시작](azure-stack-edge-return-device.md#initiate-device-return) 합니다.
3. [픽업을 예약합니다](azure-stack-edge-return-device.md#schedule-a-pickup).
4. 장치가 Microsoft에서 수신 되 면 반환 된 장치와 연결 된 [리소스를 삭제할](azure-stack-edge-return-device.md#delete-the-resource) 수 있습니다.


## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro 장치를 반환](azure-stack-edge-return-device.md)하는 방법에 대해 알아봅니다.
