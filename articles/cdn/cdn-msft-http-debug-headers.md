---
title: Microsoft의 Azure CDN에 대한 HTTP 헤더 디버그 | Microsoft Docs
description: 디버그 캐시 요청 헤더는 요청된 자산에 적용되는 캐시 정책에 대한 추가 정보를 제공합니다. 해당 헤더는 Microsoft의 Azure CDN에만 적용됩니다.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 2475bdce3ab8f153cc837601964bf4a2e90a470c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "81260414"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Microsoft의 Azure CDN에 대한 HTTP 헤더 디버그
디버그 응답 헤더인 `X-Cache`는 콘텐츠가 제공된 CDN 스택의 계층에 대한 세부 정보를 제공합니다. 해당 헤더는 Microsoft의 Azure CDN에만 적용됩니다.

### <a name="response-header-format"></a>응답 헤더 형식

헤더 | 설명
-------|------------
X-Cache: TCP_HIT | 해당 헤더는 CDN 에지 캐시에서 콘텐츠를 제공할 때 반환됩니다. 
X-Cache: TCP_REMOTE_HIT | 해당 헤더는 CDN 지역 캐시(원본 방패 계층)에서 콘텐츠를 제공할 때 반환됩니다.
X-Cache: TCP_MISS | 캐시 누락이 있는 경우 해당 헤더가 반환되고 원본에서 콘텐츠가 제공됩니다. 


