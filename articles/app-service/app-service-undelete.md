---
title: 삭제 된 App Service 앱 복원-Azure App Service
description: PowerShell을 사용 하 여 삭제 된 App Service 앱을 복원 하는 방법을 알아봅니다.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: a766f7ed7a82874df8ef7506226de0d6f38a5847
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219544"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>PowerShell을 사용 하 여 삭제 된 App Service 앱 복원

Azure App Service에서 앱을 실수로 삭제 한 경우 [Az PowerShell 모듈](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)의 명령을 사용 하 여 복원할 수 있습니다.

## <a name="list-deleted-apps"></a>삭제된 앱 나열

삭제 된 앱의 컬렉션을 가져오려면를 사용할 `Get-AzDeletedWebApp`수 있습니다.

삭제 된 특정 앱에 대 한 자세한 내용은 다음을 사용할 수 있습니다.

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app>
```

세부 정보에는 다음이 포함 됩니다.

- **DeletedSiteId**: 동일한 이름을 가진 여러 앱이 삭제 된 시나리오에 사용 되는 앱의 고유 식별자
- **SubscriptionID**: 삭제 된 리소스를 포함 하는 구독
- **위치**: 원본 앱의 위치
- **ResourceGroupName**: 원본 리소스 그룹의 이름입니다.
- **Name**: 원본 응용 프로그램의 이름입니다.
- **슬롯**: 슬롯의 이름입니다.
- **삭제 시간**: 앱이 삭제 된 시기  

## <a name="restore-deleted-app"></a>삭제 된 앱 복원

복원 하려는 앱이 식별 되 면를 사용 하 여 `Restore-AzDeletedWebApp`복원할 수 있습니다.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

명령에 대 한 입력은 다음과 같습니다.

- **리소스 그룹**: 앱이 복원 되는 대상 리소스 그룹
- **Name**: 응용 프로그램의 이름은 전역적으로 고유 해야 합니다.
- **Targetappservice계획 이름**: 앱에 연결 된 App Service 계획

기본적 `Restore-AzDeletedWebApp` 으로는 앱 구성과 콘텐츠를 모두 복원 합니다. 콘텐츠만 복원 하려면이 이상에서 `-RestoreContentOnly` 플래그를 사용 합니다.
