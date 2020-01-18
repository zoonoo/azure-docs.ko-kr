---
title: PowerShell을 사용 하 여 Azure 알림 허브 만들기 | Microsoft Docs
description: PowerShell 스크립트를 사용 하 여 Azure 알림 허브를 만드는 방법에 대해 알아봅니다.
author: dimazaid
manager: femila
services: notification-hubs
editor: sethmanheim
ms.service: notification-hubs
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2020
ms.author: dimazaid
ms.openlocfilehash: 01749e1ae5c29a7e77b2143939e25cf1ccd1aec2
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263979"
---
# <a name="use-powershell-to-create-an-azure-notification-hub"></a>PowerShell을 사용하여 Azure 알림 허브 만들기

이 샘플 PowerShell 스크립트는 샘플 Azure 알림 허브를 만듭니다. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>필수 조건

* **Azure 구독** - Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/notification-hubs/create-notification-hub/create-notification-hub.ps1 "Create a notification hub")]

## <a name="clean-up-deployment"></a>배포 정리

샘플 스크립트를 실행한 후에는 다음 명령을 사용하여 리소스 그룹 및 해당 그룹에 연결된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [새 AzNotificationHubsNamespace](/powershell/module/az.notificationhubs/new-aznotificationhubsnamespace) | 알림 허브의 네임 스페이스를 만듭니다. |
| [새 AzNotificationHub](/powershell/module/az.notificationhubs/new-aznotificationhub) | 알림 허브를 만듭니다. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.
