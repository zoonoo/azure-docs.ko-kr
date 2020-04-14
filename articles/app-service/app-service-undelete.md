---
title: 삭제된 앱 복원
description: Azure 앱 서비스에서 삭제된 앱을 복원하는 방법에 대해 알아봅니다. 실수로 삭제 된 응용 프로그램의 두통을 피하십시오.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: 296c8e2dfe99e3b0aea66f364ac6f6d9b2f60a1a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272494"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>PowerShell을 사용하여 삭제된 App Service 앱 복원

Azure 앱 서비스에서 실수로 앱을 삭제한 경우 [Az PowerShell 모듈의](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)명령을 사용하여 앱을 복원할 수 있습니다.

> [!NOTE]
> 삭제된 앱은 초기 삭제 후 30일 후에 시스템에서 제거됩니다. 앱이 제거되면 복구할 수 없습니다.
>

## <a name="re-register-app-service-resource-provider"></a>앱 서비스 리소스 공급자 다시 등록
일부 고객은 삭제된 앱 목록을 검색하는 데 실패하는 문제가 발생할 수 있습니다. 이 문제를 해결하려면 다음 명령을 실행합니다.

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>삭제된 앱 목록

삭제된 앱의 컬렉션을 얻으려면 을 `Get-AzDeletedWebApp`사용할 수 있습니다.

삭제된 특정 앱에 대한 자세한 내용은 다음을 사용할 수 있습니다.

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

자세한 정보는 다음과 같습니다.

- **DeletedSiteId**: 같은 이름의 여러 앱이 삭제 된 시나리오에 사용되는 앱의 고유 식별자
- **구독ID**: 삭제된 리소스가 포함된 구독
- **위치**: 원래 앱의 위치
- **리소스 그룹 이름**: 원래 리소스 그룹의 이름
- **이름**: 원래 앱의 이름입니다.
- **슬롯**: 슬롯의 이름입니다.
- **삭제 시간**: 앱이 삭제된 시기  

## <a name="restore-deleted-app"></a>삭제된 앱 복원

복원하려는 앱이 식별되면 을 사용하여 `Restore-AzDeletedWebApp`복원할 수 있습니다.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> 배포 슬롯은 앱의 일부로 복원되지 않습니다. 스테이징 슬롯을 복원해야 하는 `-Slot <slot-name>` 경우 플래그를 사용합니다.
>

명령의 입력은 다음과 같습니다.

- **리소스 그룹**: 앱이 복원될 대상 리소스 그룹
- **이름**: 앱의 이름은 전역적으로 고유해야 합니다.
- **TargetAppServicePlanName**: 앱에 연결된 앱 서비스 계획

기본적으로 `Restore-AzDeletedWebApp` 앱 구성과 콘텐츠를 모두 복원합니다. 콘텐츠만 복원하려면 이 명령어와 `-RestoreContentOnly` 함께 플래그를 사용합니다.

> [!NOTE]
> 앱이 앱 서비스 환경에서 호스팅된 다음 삭제된 경우 해당 앱 서비스 환경이 여전히 존재하는 경우에만 복원할 수 있습니다.
>

여기에서 전체 명령어 참조를 찾을 수 있습니다: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
