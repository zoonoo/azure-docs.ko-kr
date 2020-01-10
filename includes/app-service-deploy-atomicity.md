---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3d9b0a7f08efcf9ea5892467b638e95d0404f35f
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752259"
---
## <a name="what-happens-to-my-app-during-deployment"></a>배포하는 동안 앱에서 진행되는 작업

공식적으로 지원 되는 모든 배포 방법에서는 앱의 `/home/site/wwwroot` 폴더에 있는 파일을 변경 합니다. 이러한 파일은 앱을 실행 하는 데 사용 됩니다. 따라서 잠긴 파일로 인해 배포가 실패할 수 있습니다. 모든 파일이 동시에 업데이트 되는 것이 아니기 때문에 배포 하는 동안 앱이 예상과 다르게 동작할 수도 있습니다. 이는 고객이 직면 한 앱에는 바람직하지 않습니다. 이러한 문제를 방지하는 몇 가지 다른 방법이 있습니다.

- 배포하는 동안 앱을 중지하거나 앱에 대한 오프라인 모드를 사용하도록 설정합니다. 자세한 내용은 [배포 중 잠긴 파일 처리](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)를 참조 하세요.
- [자동 전환](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) 사용하도록 설정한 상태로 [스테이징 슬롯](../articles/app-service/deploy-staging-slots.md)에 배포합니다. 
- 연속 배포 대신 [패키지에서 실행](https://github.com/Azure/app-service-announcements/issues/84) 을 사용 합니다.
