---
title: Azure Stack Edge Pro 장치 교체 | Microsoft Docs
description: 대체 Azure Stack Edge Pro 장치를 가져오는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: ec16a2b42b818e96399b8fdbad4a0951f84ef825
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90893896"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro 장치 교체

이 문서에서는 대체 Azure Stack Edge Pro 장치를 가져오는 방법을 설명 합니다. 기존 디바이스에 하드웨어 오류가 있거나 업그레이드가 필요한 경우 교체 디바이스가 필요합니다. 


이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * 하드웨어 문제에 대 한 지원 티켓 열기
> * Azure Portal에서 대체 장치에 대 한 새 리소스를 만듭니다.
> * 교체 장치를 설치 하 고 활성화 합니다.
> * 원래 장치 반환

## <a name="open-a-support-ticket"></a>지원 티켓 열기

기존 장치에 하드웨어 오류가 있는 경우 지원 티켓을 엽니다. Microsoft 지원에서 이 인스턴스에 FRU(Field Replacement Unit)를 사용할 수 없거나 디바이스에 하드웨어 업그레이드가 필요하다고 판단합니다. 두 경우 모두 Microsoft 지원에서 대체 디바이스를 주문합니다.

1. Microsoft 지원에서 지원 티켓을 열어 디바이스 반환 의사를 밝힙니다. **Azure Stack Edge Pro 하드웨어**로 문제 유형을 선택 합니다.

    ![지원 티켓 열기](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Microsoft 지원 엔지니어가 연락드릴 것입니다. 배송 정보를 제공합니다.
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>대체 장치에 대 한 리소스 만들기

리소스를 만들려면 다음 단계를 수행 합니다.

1. [새 리소스 만들기](azure-stack-edge-deploy-prep.md#create-a-new-resource) 의 단계에 따라 교체 장치에 대 한 리소스를 만듭니다. 

2. **Azure Stack Edge Pro 장치**에 대 한 확인란을 선택 해야 합니다. 

    ![대체 장치에 대 한 리소스](media/azure-stack-edge-replace-device/replace-resource-1.png)  

## <a name="install-and-activate-the-replacement-device"></a>교체 장치 설치 및 활성화

교체 장치를 설치 하 고 활성화 하려면 다음 단계를 수행 합니다.

1. [장치를 설치](azure-stack-edge-deploy-install.md)합니다.

2. 이전에 만든 새 리소스에 대해 [장치를 활성화](azure-stack-edge-deploy-connect-setup-activate.md) 합니다.

## <a name="return-your-existing-device"></a>기존 장치 반환

모든 단계에 따라 원래 디바이스를 반환합니다.

1. [디바이스에서 데이터를 지웁니다](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. 원본 장치에 대 한 [장치 반환을 시작](azure-stack-edge-return-device.md#initiate-device-return) 합니다.
3. [픽업을 예약합니다](azure-stack-edge-return-device.md#schedule-a-pickup).
4. 반환된 디바이스와 연결된 [리소스를 삭제합니다](azure-stack-edge-return-device.md#delete-the-resource).


## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro 장치를 반환](azure-stack-edge-return-device.md)하는 방법에 대해 알아봅니다.
