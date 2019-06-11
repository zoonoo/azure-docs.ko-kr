---
title: Azure 데이터 가장자리가 상자의 장치 액세스, 기능 및 연결 모드 | Microsoft Docs
description: 액세스, 기능 및 Azure로 데이터를 전송 하는 데 도움이 됩니다 Azure 데이터 상자 Edge 장치에 대 한 연결 모드를 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 8937f4c47f0fa84d4ec371e951cff8a2fdaa8481
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476909"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Azure 데이터 상자의 가장자리에 대 한 액세스, 기능 및 연결 모드 관리

이 문서에서는 Azure 데이터 상자의 가장자리에 대 한 액세스, 기능 및 연결 모드를 관리 하는 방법을 설명 합니다. 이러한 작업은 로컬 웹 UI 또는 Azure Portal을 통해 수행합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 디바이스 액세스 관리
> * 연결 모드 관리
> * 전원 관리


## <a name="manage-device-access"></a>디바이스 액세스 관리

데이터 상자 Edge 장치에 대 한 액세스는 장치 암호를 사용 하 여 제어 됩니다. 로컬 웹 UI 통해 암호를 변경할 수 있습니다. 또한 Azure portal에서 장치 암호를 재설정할 수 있습니다.

### <a name="change-device-password"></a>디바이스 암호 변경

장치 암호를 변경 하려면 로컬 UI에 이러한 단계를 수행 합니다.

1. 로컬 웹 UI에서 **유지 관리 > 암호 변경**으로 이동합니다.
2. 현재 암호를 입력한 다음, 새 암호를 입력합니다. 제공된 암호는 8 ~ 16자 사이여야 합니다. 암호에 대문자, 소문자, 숫자, 특수 문자 중 3가지가 포함되어야 합니다. 새 암호를 확인합니다.

    ![암호 변경](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. **암호 변경**을 선택합니다.
 
### <a name="reset-device-password"></a>장치 암호 재설정

재설정 워크플로에서는 사용자가 이전 암호를 기억할 필요가 없으며 암호를 분실했을 때 유용합니다. 이 워크플로는 Azure Portal에서 수행합니다.

1. Azure Portal에서 **개요 > 관리자 암호 재설정**으로 이동합니다.

    ![암호 재설정](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. 새 암호를 입력하고 확인합니다. 제공된 암호는 8 ~ 16자 사이여야 합니다. 암호에 대문자, 소문자, 숫자, 특수 문자 중 3가지가 포함되어야 합니다. 선택 **재설정**합니다.

    ![암호 재설정](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>리소스 액세스 관리

데이터 상자 Edge/데이터 상자 게이트웨이, IoT Hub 및 Azure Storage 리소스를 만들려면 리소스 그룹 수준에서 참가자 또는 더 높은 권한이 필요 합니다. 해당 리소스 공급자를 등록 해야 합니다. 정품 인증 키 및 자격 증명을 포함 하는 모든 작업에 대 한 권한이 Azure Active Directory Graph API에도 필요 합니다. 이러한 작업은 다음 섹션에 설명 되어 있습니다.

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>Microsoft Azure Active Directory Graph API 사용 권한 관리

데이터 상자에 지 장치 또는 자격 증명을 필요로 하는 작업을 수행 하는 것에 대 한 정품 인증 키를 생성 하는 경우 Azure Active Directory Graph API 사용 권한이 필요 합니다. 작업 자격 증명을 할 수 있습니다.

-  연결 된 저장소 계정으로 공유를 만드는 중입니다.
-  장치에서 공유에 액세스할 수 있는 사용자를 만드는 중입니다.

해야는 `User` Active Directory 테 넌 트에 액세스할 수 있으려면 필요에 따라 `Read all directory objects`합니다. 권한이 없기 때문에 게스트 사용자 일 수 없습니다 `Read all directory objects`합니다. 게스트에서 다음 데이터 상자에 지 장치에서 공유를 생성 하는 정품 인증 키를 생성 하는 등 작업 인 경우 사용자의 모든 만들어지지 것입니다.

Azure Active Directory Graph API에는 사용자에 게 액세스를 제공 하는 방법에 대 한 자세한 내용은 참조 하세요. [관리자, 사용자 및 게스트 사용자에 대 한 액세스를 기본](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-)입니다.

### <a name="register-resource-providers"></a>리소스 공급자 등록

(Azure Resource Manager 모델)에서 Azure에서 리소스를 프로 비전 하려면 해당 리소스의 생성을 지 원하는 리소스 공급자를 해야 합니다. 예를 들어, 가상 컴퓨터를 프로 비전 하려면 있어야 'Microsoft.Compute' 리소스 공급자 구독에서 사용할 수 있습니다.
 
구독 수준에서 리소스 공급자 등록 됩니다. 기본적으로 모든 새 Azure 구독 목록을 자주 사용 되는 리소스 공급자를 사용 하 여 미리 등록 됩니다. 'Microsoft.DataBoxEdge'에 대 한 리소스 공급자는이 목록에 포함 되지 않습니다.

사용자가 이러한 리소스에 대 한 리소스 공급자가 이미 있다면, 소유자 권한이 있는 해당 리소스 그룹 내에서 'Microsoft.DataBoxEdge'와 같은 리소스를 만들 수에 대 한 구독 수준에 대 한 액세스 권한을 부여 하지 않아도 됩니다. 등록 합니다.

모든 리소스를 만들려면 먼저 리소스 공급자 구독에 등록 되어 있는지 확인 합니다. 리소스 공급자 등록 되지 않은 경우에 새 리소스를 만드는 사용자에 구독 수준에서 필요한 리소스 공급자를 등록할 수 있는 충분 한 권한이 있는지 확인 해야 합니다. 그런 다음이 수행 하지 않은, 다음 오류를 볼 수 있습니다.

*구독 <Subscription name> 리소스 공급자를 등록할 수 있는 권한이 없습니다. Microsoft.DataBoxEdge.*


현재 구독에 등록 된 리소스 공급자의 목록을 가져오려면, 다음 명령을 실행 합니다.

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

데이터 상자 Edge 장치에 대 한 `Microsoft.DataBoxEdge` 등록 해야 합니다. 등록 하려면 `Microsoft.DataBoxEdge`, 구독 관리자는 다음 명령을 실행 해야 합니다.

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

리소스 공급자를 등록 하는 방법에 대 한 자세한 내용은 참조 하세요. [리소스 공급자 등록 오류 해결](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors)합니다.

## <a name="manage-connectivity-mode"></a>연결 모드 관리

완전히 연결 된 기본 모드 외에도 장치도 부분적으로 연결 된 또는 완벽 하 게 연결이 끊어진 모드에서 실행할 수 있습니다. 이러한 각 모드는 아래에 설명되어 있습니다.

- **완전히 연결** -장치가 작동 하는 일반 기본 모드입니다. 클라우드 업로드 및 다운로드 데이터는이 모드에서 사용 됩니다. 장치를 관리 하는 Azure portal 또는 로컬 웹 UI 사용할 수 있습니다.

- **부분적으로 연결이 끊긴** – 장치가이 모드에서 업로드 하거나 Azure portal을 통해 관리할 수 있지만 데이터 공유를 다운로드 합니다.

    이 모드는 일반적으로 요금제 위성 네트워크에서 사용되며 목표는 네트워크 대역폭 소비를 최소화하는 것입니다. 디바이스 모니터링 작업을 위해 최소한의 네트워크 사용량이 여전히 발생할 수 있습니다.

- **연결 끊김** – 이 모드에서는 디바이스가 클라우드에서 연결이 완전히 끊어지고 클라우드 업로드와 다운로드를 모두 사용할 수 없습니다. 디바이스는 로컬 웹 UI를 통해서만 관리할 수 있습니다.

    이 모드는 일반적으로 디바이스를 오프라인 상태로 만들 때 사용합니다.

디바이스 모드를 변경하려면 다음 단계를 수행합니다.

1. 디바이스의 로컬 웹 UI에서 **구성 > 클라우드 설정**으로 이동합니다.
2. 드롭다운 목록에서에서 장치를 작동 하려면 원하는 모드를 선택 합니다. 선택할 수 있습니다 **완벽 하 게 연결**를 **부분적으로 연결 됨**, 및 **완벽 하 게 연결이 끊어진**합니다. 부분적으로 연결이 끊긴 모드에서 디바이스를 실행하려면 **Azure Portal 관리**를 사용하도록 설정합니다.

    ![연결 모드](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>전원 관리

종료 하거나 물리적 장치의 로컬 웹 UI를 사용 하 여 다시 시작할 수 있습니다. 에서는 다시 시작 하기 전에 데이터 서버와 장치에서 공유를 오프 라인을 수행 하는 것이 좋습니다. 이 작업을 수행하면 데이터 손상 가능성이 최소화됩니다.

1. 로컬 웹 UI에서 **유지 관리 > 전원 설정**으로 이동합니다.
2. 선택 **종료** 하거나 **다시 시작** 할 내용에 따라 합니다.

    ![전원 설정](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 확인 메시지가 표시 되 면 선택 **예** 진행 합니다.

> [!NOTE]
> 물리적 장치를 종료 하는 경우 전원 단추를 설정 하려면 장치에 푸시 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [공유 관리](data-box-edge-manage-shares.md) 방법에 대해 알아봅니다.