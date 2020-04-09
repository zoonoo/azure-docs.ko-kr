---
title: Azure 정문에서 사용자 지정 HTTPS를 사용하도록 설정하는 CA 허용
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874673"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Azure 정문에서 사용자 지정 HTTPS를 사용하도록 설정하기 위한 허용된 인증 기관

Azure Front Door 사용자 지정 도메인의 경우 [자체 인증서를 사용하여 HTTPS 기능을 사용하도록 설정하면](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)허용된 인증 기관(CA)을 사용하여 TLS/SSL 인증서를 만들어야 합니다. 그렇지 않고 허용되지 않는 CA 또는 자체 서명 인증서를 사용할 경우 요청이 거부됩니다.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
