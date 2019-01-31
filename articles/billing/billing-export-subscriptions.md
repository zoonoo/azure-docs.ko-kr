---
title: 최상위 Azure 구독 정보 내보내기 | Microsoft Docs
description: 계정과 연결된 모든 Azure 구독 ID를 보는 방법을 설명합니다.
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: a2716793a47eae56a8e6eef8416ff446b6f7176f
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902570"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>최상위 구독 정보 내보내기 및 보기
사용자 자격 증명과 연결된 구독 ID 집합을 확인할 필요가 있으면 [Azure 계정 센터에서 구독 정보를 사용하여 .json 파일을 다운로드](http://account.azure.com/subscriptions/download)합니다.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

다운로드한 .json 파일에서 제공하는 정보는 다음과 같습니다.
- 전자 메일: 계정과 연결된 이메일 주소입니다.
- Puid: 결제 계정과 연결된 고유 식별자입니다.
- SubscriptionIds: 구독 ID별로 열거된 계정에 속한 구독 목록입니다.

### <a name="subscriptionsjson-sample"></a>subscriptions.json 샘플

```json
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
```
