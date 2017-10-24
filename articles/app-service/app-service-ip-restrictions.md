---
title: "Azure App Service IP 제한 | Microsoft Docs"
description: "Azure App Service에 IP 제한을 사용하는 방법"
author: btardif
manager: stefsch
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/12/2017
ms.author: byvinyal
ms.openlocfilehash: 54c0c5050c812c2a59631541d94c553974acd2f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-static-ip-restrictions"></a>Azure App Service 고정 IP 제한 #

IP 제한을 사용하여 앱 액세스가 허용되는 IP 주소 목록을 정의할 수 있습니다. 허용 목록에는 서브넷 마스크에 의해 정의되는 개별 IP 주소 또는 IP 주소 범위를 포함할 수 있습니다.

클라이언트에서 앱에 대한 요청이 생성되면 허용 목록에 대해 IP 주소가 평가됩니다. IP 주소가 목록에 없는 경우 앱은 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 상태 코드로 응답합니다.

IP 제한은 앱에 할당된 것과 동일한 App Service 계획 인스턴스에서 평가됩니다.

앱에 IP 제한 규칙을 추가하려면 메뉴를 사용하여 **네트워크**>**IP 제한**을 열고 **IP 제한 구성**을 클릭합니다.

![IP restrictions] (media/app-service-ip-restrictions/ip-restrictions.png)

여기서 앱에 대해 정의된 IP 제한 규칙 목록을 검토할 수 있습니다.

![IP 제한 나열](media/app-service-ip-restrictions/browse-ip-restrictions.png)

**[+] 추가**를 클릭하여 새 IP 제한 규칙을 추가할 수 있습니다.

![IP 제한 추가](media/app-service-ip-restrictions/add-ip-restrictions.png)
