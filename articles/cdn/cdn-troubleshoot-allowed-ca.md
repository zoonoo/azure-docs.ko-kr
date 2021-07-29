---
title: 사용자 지정 HTTPS를 사용하기 위해 허용된 CA
titleSuffix: Azure Content Delivery Network
description: 사용자 고유의 인증서를 사용하여 사용자 지정 도메인에서 HTTPS를 사용하도록 설정하려면 허용되는 CA(인증 기관)를 사용하여 인증서를 만들어야 합니다.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f98e28c89fa70831108cfbbbaca6e2f316d1b039
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112003631"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https"></a>사용자 지정 HTTPS를 사용하기 위해 허용되는 인증 기관

Azure CDN(Content Delivery Network) 사용자 지정 도메인에 대해 [ 사용자 고유의 인증서를 사용하여 HTTPS 기능을 사용하도록 설정](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates)하는 경우 특정 인증서 요구 사항이 필요합니다. 

* **Microsoft의 Azure CDN 표준** 프로필에는 다음 목록에 있는 승인된 CA(인증 기관) 중 한 곳의 인증서가 필요합니다. 승인되지 않은 CA의 인증서 또는 자체 서명된 인증서를 사용하는 경우에는 요청이 거부됩니다. 

* **Verizon의 Azure CDN 표준** 및 **Verizon의 Azure CDN 프리미엄** 프로필은 유효한 CA의 유효한 인증서를 허용합니다. Verizon 프로필은 자체 서명된 인증서를 지원하지 않습니다.

> [!NOTE]
> 사용자 고유의 인증서를 사용하여 사용자 지정 도메인 HTTPS 기능을 사용하도록 설정하는 옵션은 **Akamai의 Azure CDN 표준** 프로필에 사용할 수 *없습니다*. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

