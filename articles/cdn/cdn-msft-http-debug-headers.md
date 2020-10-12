---
title: Microsoft의 Azure CDN에 대 한 HTTP 헤더 디버그 | Microsoft Docs
description: 디버그 캐시 요청 헤더는 요청 된 자산에 적용 되는 캐시 정책에 대 한 추가 정보를 제공 합니다. 이러한 헤더는 Microsoft의 Azure CDN에만 적용 됩니다.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81260414"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Microsoft의 Azure CDN에 대 한 디버그 HTTP 헤더
디버그 응답 헤더는 콘텐츠가 제공 된 `X-Cache` CDN 스택의 계층에 대 한 세부 정보를 제공 합니다. 이 헤더는 Microsoft의 Azure CDN에만 적용 됩니다.

### <a name="response-header-format"></a>응답 헤더 형식

헤더 | 설명
-------|------------
X 캐시: TCP_HIT | 이 헤더는 CDN에 지 캐시에서 콘텐츠를 제공할 때 반환 됩니다. 
X 캐시: TCP_REMOTE_HIT | 이 헤더는 CDN 지역 캐시 (원본 방패 계층)에서 콘텐츠를 제공할 때 반환 됩니다.
X 캐시: TCP_MISS | 캐시 누락이 있는 경우이 헤더가 반환 되 고 원본에서 콘텐츠가 제공 됩니다. 


