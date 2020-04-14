---
title: 마이크로소프트에서 Azure CDN에 대 한 HTTP 헤더 디버그 | 마이크로 소프트 문서
description: 디버그 캐시 요청 헤더는 요청된 자산에 적용되는 캐시 정책에 대한 추가 정보를 제공합니다. 이러한 헤더는 Microsoft의 Azure CDN에만 해당됩니다.
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
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260414"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>마이크로소프트에서 Azure CDN에 대 한 디버그 HTTP 헤더
디버그 응답 헤더는 `X-Cache`콘텐츠가 제공된 CDN 스택의 계층에 대한 세부 정보를 제공합니다. 이 헤더는 Microsoft의 Azure CDN에만 해당됩니다.

### <a name="response-header-format"></a>응답 헤더 형식

헤더 | Description
-------|------------
X 캐시: TCP_HIT | 이 헤더는 CDN 에지 캐시에서 콘텐츠가 제공될 때 반환됩니다. 
X 캐시: TCP_REMOTE_HIT | 이 헤더는 CDN 지역 캐시(Origin 쉴드 계층)에서 콘텐츠가 제공될 때 반환됩니다.
X 캐시: TCP_MISS | 이 헤더는 캐시 누락이 있을 때 반환되며 콘텐츠는 Origin에서 제공됩니다. 


