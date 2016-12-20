---
title: "Azure 앱 서비스의 WebJobs"
description: "백그라운드 테스트를 실행하는 WebJobs를 빌드하는 방법, 저장소 및 서비스 버스와 같은 서비스와 상호 작용하는 방법 및 예약된 작업을 만드는 방법을 알아봅니다."
services: app-service
documentationcenter: 
author: christopheranderson
manager: wpickett
editor: mollybos
ms.assetid: 85975432-04c9-4b83-b937-b30c082d52a1
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2015
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 45016e09e981bab6e4413048838c66f1108dc663


---
# <a name="using-webjobs-in-azure-app-service"></a>Azure 앱 서비스에서 WebJobs 사용
이 문서는 Azure WebJobs 및 Azure WebJobs SDK를 사용하는 방법에 대한 설명서 리소스에 연결됩니다. Azure WebJob은 쉽게 스크립트 또는 프로그램을 [앱 서비스 웹앱](http://go.microsoft.com/fwlink/?LinkId=529714)에서 백그라운드 프로세스로 실행하는 편리한 방법을 제공합니다. cmd, bat, exe(.NET), ps1, sh, php, py, js 및 jar와 같은 실행 파일을 업로드하고 실행할 수 있습니다. 이러한 프로그램은 일정에 따라(cron) 또는 지속적으로 WebJob으로 실행됩니다.

WebJobs SDK를 사용하면 Azure 저장소를 보다 쉽게 사용할 수 있습니다. WebJobs SDK에는 Microsoft Azure 저장소 Blob, 큐 및 테이블 뿐만 아니라 서비스 버스 큐에서 작동하는 바인딩 및 트리거 시스템이 있습니다.

Visual Studio의 통합 도구를 사용하면 WebJob을 원활하게 만들고, 배포하고, 관리할 수 있습니다. 템플릿에서 WebJob을 만들고, 게시하고 관리(실행/중지/모니터링/디버깅)할 수 있습니다.

Azure 포털의 WebJob 대시보드는 WebJob 내의 개별 함수를 호출하는 기능을 비롯하여 WebJob의 실행을 완전히 제어할 수 있는 강력한 관리 기능을 제공합니다. 또한 대시보드에는 런타임 함수 및 로깅 출력도 표시됩니다.

[!INCLUDE [app-service-blueprint-webjobs](../../includes/app-service-blueprint-webjobs.md)]




<!--HONumber=Nov16_HO3-->


