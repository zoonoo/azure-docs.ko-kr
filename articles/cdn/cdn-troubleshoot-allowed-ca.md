---
title: 사용자 지정 HTTPS를 사용 하도록 허용 된 CA
titleSuffix: Azure Content Delivery Network
description: 사용자 고유의 인증서를 사용 하 여 사용자 지정 도메인에서 HTTPS를 사용 하는 경우 허용 되는 CA (인증 기관)를 사용 하 여 인증서를 만들어야 합니다.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f98e28c89fa70831108cfbbbaca6e2f316d1b039
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99573401"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https"></a>사용자 지정 HTTPS를 사용 하기 위해 허용 된 인증 기관

Content Delivery Network (Azure CDN) 사용자 지정 도메인에 대 한 사용자 [고유의 인증서를 사용 하 여 HTTPS 기능을 사용 하도록 설정](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) 하는 경우 특정 인증서 요구 사항이 필요 합니다. 

* **Microsoft 프로필의 Azure CDN 표준** 에는 다음 목록에 승인 된 CA (인증 기관) 중 하나의 인증서가 필요 합니다. 승인 되지 않은 CA의 인증서 또는 자체 서명 된 인증서를 사용 하는 경우 요청이 거부 됩니다. 

* **Verizon의 Azure CDN 표준과** **Verizon의 Azure CDN Premium** 프로필은 유효한 CA의 모든 유효한 인증서를 허용 합니다. Verizon 프로필은 자체 서명 된 인증서를 지원 하지 않습니다.

> [!NOTE]
> 사용자 고유의 인증서를 사용 하 여 사용자 지정 도메인 HTTPS 기능을 사용 하도록 설정 하는 옵션은 **Akamai의 Azure CDN Standard** 프로필에 사용할 수 *없습니다* . 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

