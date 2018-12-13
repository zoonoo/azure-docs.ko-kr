---
title: Microsoft Azure Data Box Gateway 디바이스 액세스, 전원 및 연결 모드 | Microsoft Docs
description: Azure로 데이터를 전송하는 데 도움이 되는 Azure Data Box Gateway 디바이스에 대한 액세스, 전원 및 연결 모드를 관리하는 방법에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/09/2018
ms.author: alkohli
ms.openlocfilehash: 8f9172418f15b129a71242038efd4cdb7683bbf7
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516543"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway-preview"></a>Azure Data Box Gateway(미리 보기)에 대한 액세스, 전원 및 연결 모드 관리

이 문서에서는 Azure Data Box Gateway에 대한 액세스, 전원 및 연결 모드를 관리하는 방법에 대해 설명합니다. 이러한 작업은 로컬 웹 UI 또는 Azure Portal을 통해 수행합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 디바이스 액세스 관리
> * 연결 모드 관리
> * 전원 관리

> [!IMPORTANT]
> Data Box Gateway는 미리 보기로 제공되고 있습니다. 이 솔루션을 주문하고 배포하기 전에 [미리 보기에 대한 Azure 서비스 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 검토하세요.

## <a name="manage-device-access"></a>디바이스 액세스 관리

Data Box Gateway에 대한 액세스는 디바이스 관리자 암호를 사용하여 제어합니다. 로컬 웹 UI를 통해 관리자 암호를 변경할 수 있습니다. 또한 Azure Portal에서 디바이스 관리자 암호를 다시 설정할 수도 있습니다.

### <a name="change-device-administrator-password"></a>디바이스 관리자 암호 변경

암호를 잊은 경우 암호를 변경할 수 있습니다. 로컬 웹 UI에서 다음 단계에 따라 디바이스 관리자 암호를 변경합니다.

1. 로컬 웹 UI에서 **유지 관리 > 암호 변경**으로 이동합니다.
2. 현재 암호를 입력한 다음, 새 암호를 입력합니다. 제공된 암호는 8 ~ 16자 사이여야 합니다. 암호에 대문자, 소문자, 숫자, 특수 문자 중 3가지가 포함되어야 합니다. 새 암호를 확인합니다.

    ![암호 변경](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. **암호 변경**을 클릭합니다.
 
### <a name="reset-device-administrator-password"></a>디바이스 관리자 암호 다시 설정

재설정 워크플로에서는 사용자가 이전 암호를 기억할 필요가 없으며 암호를 분실했을 때 유용합니다. 이 워크플로는 Azure Portal에서 수행합니다.

1. Azure Portal에서 **개요 > 관리자 암호 재설정**으로 이동합니다.

    ![암호 재설정](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. 새 암호를 입력하고 확인합니다. 제공된 암호는 8 ~ 16자 사이여야 합니다. 암호에 대문자, 소문자, 숫자, 특수 문자 중 3가지가 포함되어야 합니다. **재설정**을 클릭합니다.

    ![암호 재설정](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>연결 모드 관리

디바이스는 기본 표준 모드 외에도, 부분적으로 연결이 끊어져 있거나 연결이 끊긴 모드에서도 실행할 수 있습니다. 이러한 각 모드는 아래에 설명되어 있습니다.

- **부분적으로 연결이 끊어짐** - 이 모드에서 디바이스는 일부 데이터를 공유에 업로드할 수 없지만 Azure Portal을 통해 관리할 수 있습니다.

    이 모드는 일반적으로 요금제 위성 네트워크에서 사용되며 목표는 네트워크 대역폭 소비를 최소화하는 것입니다. 디바이스 모니터링 작업을 위해 최소한의 네트워크 사용량이 여전히 발생할 수 있습니다.

- **연결 끊김** – 이 모드에서는 디바이스가 클라우드에서 연결이 완전히 끊어지고 클라우드 업로드와 다운로드를 모두 사용할 수 없습니다. 디바이스는 로컬 웹 UI를 통해서만 관리할 수 있습니다.

    이 모드는 일반적으로 디바이스를 오프라인 상태로 만들 때 사용합니다.

디바이스 모드를 변경하려면 다음 단계를 수행합니다.

1. 디바이스의 로컬 웹 UI에서 **구성 > 클라우드 설정**으로 이동합니다.
2. **클라우드 업로드 및 다운로드**를 사용하지 않도록 설정합니다.
3. 부분적으로 연결이 끊긴 모드에서 디바이스를 실행하려면 **Azure Portal 관리**를 사용하도록 설정합니다.

    ![연결 모드](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. 연결이 끊긴 모드에서 디바이스를 실행하려면 **Azure Portal 관리**를 사용하지 않도록 설정합니다. 이제 디바이스는 로컬 웹 UI를 통해서만 관리할 수 있습니다.

    ![연결 모드](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>전원 관리

로컬 웹 UI를 사용하여 실제 및 가상 디바이스를 종료하거나 다시 시작할 수 있습니다. 다시 시작하기 전에 호스트에서 공유를 오프라인으로 전환한 후 디바이스를 다시 시작하는 것이 좋습니다. 이 작업을 수행하면 데이터 손상 가능성이 최소화됩니다.

1. 로컬 웹 UI에서 **유지 관리 > 전원 설정**으로 이동합니다.
2. 수행하려는 작업에 따라 **종료** 또는 **다시 시작**을 클릭합니다.

    ![전원 설정](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 확인 메시지가 표시되면 **예**를 클릭하여 계속 진행합니다.

> [!NOTE]
> 가상 디바이스를 종료하는 경우 하이퍼바이저 관리를 통해 디바이스를 시작해야 합니다.