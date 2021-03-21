---
title: Azure 전면 도어 표준/프리미엄 (미리 보기)에 대해 허용 된 인증 기관
description: 이 문서에는 사용자 고유의 인증서를 만들 때 허용 되는 모든 인증 기관이 나열 됩니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: e31ad0734630fbd0b4960c26f8d562cea66ae675
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434869"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>Azure 전면 도어 표준/프리미엄 (미리 보기)에 대해 허용 된 인증 기관

Azure Front 도어 표준/프리미엄 사용자 지정 도메인에 대해 사용자 고유의 인증서를 사용 하 여 HTTPS 기능을 사용 하도록 설정 하는 경우 TLS/SSL 인증서를 만들려면 허용 된 CA (인증 기관)가 필요 합니다. 그렇지 않고 허용되지 않는 CA 또는 자체 서명 인증서를 사용할 경우 요청이 거부됩니다.

[!INCLUDE [cdn-front-door-allowed-ca](../../../includes/cdn-front-door-allowed-ca.md)]
