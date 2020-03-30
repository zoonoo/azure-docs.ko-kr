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
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75945172"
---
## <a name="what-happens-to-my-app-during-deployment"></a>배포하는 동안 앱에서 진행되는 작업

공식적으로 지원되는 모든 배포 방법은 앱 `/home/site/wwwroot` 폴더의 파일을 변경합니다. 이러한 파일은 앱을 실행하는 데 사용됩니다. 따라서 잠긴 파일로 인해 배포가 실패할 수 있습니다. 모든 파일이 동시에 업데이트되지 는 않으므로 배포 중에 앱이 예기치 않게 동작할 수도 있습니다. 이는 고객 대면 앱에는 바람직하지 않습니다. 이러한 문제를 방지하는 몇 가지 다른 방법이 있습니다.

- 압축을 풀지 않고 [ZIP 패키지에서 직접 앱을 실행합니다.](../articles/app-service/deploy-run-package.md)
- 배포하는 동안 앱을 중지하거나 앱에 대한 오프라인 모드를 사용하도록 설정합니다. 자세한 내용은 [배포 중에 잠긴 파일 거래를](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)참조하십시오.
- [자동 전환](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) 사용하도록 설정한 상태로 [스테이징 슬롯](../articles/app-service/deploy-staging-slots.md)에 배포합니다. 
