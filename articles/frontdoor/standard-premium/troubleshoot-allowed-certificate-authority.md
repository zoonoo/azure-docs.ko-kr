---
title: Azure Front Door Standard/Premium(미리 보기)에 허용된 인증 기관
description: 이 문서에는 사용자 고유의 인증서를 만들 때 허용되는 모든 인증 기관이 나와 있습니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: e31ad0734630fbd0b4960c26f8d562cea66ae675
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112003636"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>Azure Front Door Standard/Premium(미리 보기)에 허용된 인증 기관

Azure Front Door Standard/Premium 사용자 지정 도메인에 대해 사용자 고유의 인증서를 통해 HTTPS 기능을 사용하도록 설정하는 경우 TLS/SSL 인증서를 만들려면 허용된 CA(인증 기관)가 필요합니다. 그렇지 않고 허용되지 않는 CA 또는 자체 서명 인증서를 사용할 경우 요청이 거부됩니다.

[!INCLUDE [cdn-front-door-allowed-ca](../../../includes/cdn-front-door-allowed-ca.md)]
