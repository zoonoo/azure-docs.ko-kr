---
title: Azure CDN에서 사용자 지정 HTTPS를 사용 하도록 허용 된 CA
description: 고유의 인증서를 사용하여 사용자 지정 도메인에서 HTTPS를 사용하도록 설정하려면 허용되는 CA(인증 기관)를 사용하여 인증서를 만들어야 합니다.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/18/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 29b6cb25e021e86ce6663b4db5c89217aaf70a37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84887394"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Azure CDN에서 사용자 지정 HTTPS를 사용하기 위해 허용되는 인증 기관

Azure Content Delivery Network (CDN) 사용자 지정 도메인에 대 한 [자체 인증서를 사용 하 여 HTTPS 기능을 사용 하도록 설정 하는](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) 경우 특정 인증서 요구 사항을 충족 해야 합니다. **Microsoft 프로필의 Azure CDN 표준** 에는 다음 목록에 승인 된 CA (인증 기관) 중 하나의 인증서가 필요 합니다. 승인 되지 않은 CA의 인증서 또는 자체 서명 된 인증서를 사용 하는 경우 요청이 거부 됩니다. **Verizon의 Azure CDN 표준과** **Verizon의 Azure CDN Premium** 프로필은 유효한 CA의 모든 유효한 인증서를 허용 합니다.

> [!NOTE]
> 사용자 고유의 인증서를 사용 하 여 사용자 지정 도메인 HTTPS 기능을 사용 하도록 설정 하는 옵션은 **Akamai의 Azure CDN Standard** 프로필에 사용할 수 *없습니다* . 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

