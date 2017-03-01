---
title: "Azure 앱 서비스에 응용 프로그램 배포"
description: "앱 서비스 작업에 응용 프로그램 배포 방법 알아보기"
keywords: "앱 서비스, azure 앱 서비스, 배포, 배포"
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: 
ms.assetid: de12cd6e-e124-4e48-90bc-c3a3801305da
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2778057e772fcba7e6021ac11ec5bdcf3720a703
ms.lasthandoff: 11/17/2016


---
# <a name="azure-app-service-deployment-overview"></a>Azure 앱 서비스 배포 개요
Azure 앱 서비스는 강력하고 유연한 배포 워크플로 만들기를 지원하는 풍부하고 통합된 기능을 제공합니다. 앱 배포에서 지속적인 통합 또는 로컬 소스 제어 게시, WebDeploy 및 FTP를 포함하는 옵션을 활용할 수 있습니다. 프로덕션 앱 배포를 위해 권장되는 방법은 배포 슬롯을 교환하는 것입니다. 배포 슬롯은 프로덕션 앱과 연결된 준비 및 통합 환경을 나타냅니다. 배포 슬롯은 유효성 검사를 위해 웹 트래픽을 대상으로 구성할 수 있으며 프로덕션에 대한 배포가 필요할 때 가동 중지 시간 없이 자동으로 준비된 상태로 교환할 수 있습니다. 배포 워크플로 단계는 Visual Studio Release Management와 같은 릴리스 관리 제품을 통해 쉽게 자동화할 수 있습니다. 이는 배포의 여러 단위에서 다른 솔루션 리소스(예: 데이터 저장소)와 조정, 되풀이 및 복제에 유용합니다. 

[!INCLUDE [app-service-blueprint-deployment](../../includes/app-service-blueprint-deployment.md)]


