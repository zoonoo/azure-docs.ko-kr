---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93332002cd2ac513d125e0f9eb75dff4a2d8760c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836807"
---
## <a name="what-happens-to-my-app-during-deployment"></a>배포하는 동안 앱에서 진행되는 작업

모든 공식적으로 지원 되는 배포 방법의 파일에 변경 된 `/home/site/wwwroot` 앱의 폴더입니다. 이러한 파일은 프로덕션 환경에서 실행 되는 것과 같은. 따라서 배포는 잠긴된 파일로 인해 실패할 수 있습니다. 일부 파일이 동시에 업데이트 되므로 프로덕션 환경에서 앱 배포 하는 동안 예기치 않게 동작할 수도 수 있습니다. 이러한 문제를 방지하는 몇 가지 다른 방법이 있습니다.

- 배포하는 동안 앱을 중지하거나 앱에 대한 오프라인 모드를 사용하도록 설정합니다. 자세한 내용은 [배포 하는 동안 잠긴된 파일을 처리할](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)합니다.
- [자동 전환](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) 사용하도록 설정한 상태로 [스테이징 슬롯](../articles/app-service/deploy-staging-slots.md)에 배포합니다. 
- 사용 하 여 [패키지에서 실행](https://github.com/Azure/app-service-announcements/issues/84) 연속 배포 하는 대신 합니다.
