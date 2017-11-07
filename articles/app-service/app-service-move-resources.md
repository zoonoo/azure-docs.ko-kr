---
title: "다른 리소스 그룹으로 Azure Web App 이동 | Microsoft Docs"
description: "하나의 리소스 그룹에서 다른 리소스 그룹으로 웹앱 및 앱 서비스를 이동할 수 있는 시나리오를 설명합니다."
services: app-service
documentationcenter: 
author: ZainRizvi
manager: erikre
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: zarizvi
ms.openlocfilehash: 4d5d6540dd0b51a4f172649a1fd9a2343efeceee
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2017
---
# <a name="move-an-azure-web-app-to-another-resource-group"></a>다른 리소스 그룹으로 Azure Web App 이동
Web App 및/또는 이와 관련된 리소스를 동일한 구독의 다른 리소스 그룹 또는 다른 구독의 리소스 그룹으로 이동할 수 있습니다. 이는 Azure의 표준 리소스 관리의 일부로 수행됩니다. 자세한 내용은 [새 구독 또는 리소스 그룹으로 Azure 리소스 이동](../azure-resource-manager/resource-group-move-resources.md)을 참조하세요.

## <a name="limitations-when-moving-within-the-same-subscription"></a>동일한 구독 내 이동 시의 제한 사항

_동일한 구독 내에서_ Web App을 이동할 때 업로드된 SSL 인증서는 이동할 수 없습니다. 그러나 업로드된 SSL 인증서를 이동하지 않고 Web App을 새 리소스 그룹으로 이동할 수 있으며 앱의 SSL 기능도 계속 작동합니다. 

Web App을 사용하여 SSL 인증서를 이동하려면 다음 단계를 수행합니다.

1.  Web App에서 업로드된 인증서를 삭제합니다.
2.  Web App을 이동합니다.
3.  이동된 Web App에 인증서를 업로드합니다.

## <a name="limitations-when-moving-across-subscriptions"></a>구독 간 이동 시의 제한 사항

_구독 간에_ Web App을 이동할 때 적용되는 제한 사항은 다음과 같습니다.

- 대상 리소스 그룹에 기존 App Service 리소스가 없어야 합니다. App Service 리소스에는 다음이 포함됩니다.
    - 웹앱
    - 앱 서비스 계획
    - 업로드되거나 가져온 SSL 인증서
    - 앱 서비스 환경
- 리소스 그룹의 모든 App Service 리소스는 함께 이동해야 합니다.
- App Service 리소스는 처음 만들었던 리소스 그룹에서만 이동할 수 있습니다. App Service 리소스가 원래의 리소스 그룹에 더 이상 없으면 먼저 원래의 리소스 그룹으로 다시 이동해야 합니다. 그런 다음 구독 간에 App Service 리소스를 이동하면 됩니다. 
