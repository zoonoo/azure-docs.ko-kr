---
title: Azure Front 도어에서 사용자 지정 HTTPS를 사용 하도록 허용 된 CA
description: 고유의 인증서를 사용하여 사용자 지정 도메인에서 HTTPS를 사용하도록 설정하려면 허용되는 CA(인증 기관)를 사용하여 인증서를 만들어야 합니다.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 611f5730afed4c3a84b81d6acfd33b633c532bbc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80874673"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Azure 전면 도어에서 사용자 지정 HTTPS를 사용 하기 위해 허용 된 인증 기관

Azure Front 도어 사용자 지정 도메인의 경우 사용자 [고유의 인증서를 사용 하 여 HTTPS 기능을 사용 하도록 설정](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)하는 경우 허용 되는 CA (인증 기관)를 사용 하 여 TLS/SSL 인증서를 만들어야 합니다. 그렇지 않고 허용되지 않는 CA 또는 자체 서명 인증서를 사용할 경우 요청이 거부됩니다.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
