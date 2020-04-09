---
title: Azure CDN에서 사용자 지정 HTTPS를 사용하도록 설정하기 위한 CA 허용
description: 고유의 인증서를 사용하여 사용자 지정 도메인에서 HTTPS를 사용하도록 설정하려면 허용되는 CA(인증 기관)를 사용하여 인증서를 만들어야 합니다.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 81d2209c4b76db685e5a8d2625c84469d5c3dc43
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985850"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Azure CDN에서 사용자 지정 HTTPS를 사용하기 위해 허용되는 인증 기관

CDN(Azure Content Delivery Network) 사용자 지정 도메인에 대한 [자체 인증서를 사용하여 HTTPS 기능을 활성화할](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) 때 특정 인증서 요구 사항을 충족해야 합니다. Microsoft 프로필의 **Azure CDN 표준에는** 다음 목록에서 승인된 인증 기관(CA) 중 하나의 인증서가 필요합니다. 승인되지 않은 CA의 인증서또는 자체 서명된 인증서를 사용하는 경우 요청이 거부됩니다. **버라이존 및 버라이존 프로필에서** **Azure CDN 프리미엄에서** Azure CDN 표준 유효한 CA에서 유효한 인증서를 허용 합니다.

> [!NOTE]
> 사용자 지정 도메인 HTTPS 기능을 사용하도록 설정하기 위해 자체 인증서를 사용하는 옵션은 **Akamai 프로필의 Azure CDN 표준에서** 사용할 수 *없습니다.* 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

