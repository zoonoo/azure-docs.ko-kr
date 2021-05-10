---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "70737494"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>브라우저에서 Azure Files를 사용할 때 웹앱에서 ConditionHeadersNotSupported 오류 발생

ConditionHeadersNotSupported 오류는 웹 브라우저와 같은 조건부 헤더를 사용하는 애플리케이션를 통해 Azure Files에 호스팅된 콘텐츠에 액세스할 때 액세스에 실패하는 경우 발생합니다. 오류는 조건부 헤더가 지원되지 않음을 명시합니다.

![Azure Files 조건부 헤더 오류](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>원인

조건부 헤더는 아직 지원되지 않습니다. 이를 구현하는 애플리케이션은 파일이 액세스될 때마다 전체 파일을 요청해야 합니다.

### <a name="workaround"></a>해결 방법

새 파일이 업로드되면 cache-control 속성은 기본적으로 “no-cache”입니다. 애플리케이션이 매번 파일을 요청하도록 하려면 파일의 cache-control 속성을 “no-cache”에서 “no-cache, no-store, must-revalidate”로 업데이트해야 합니다. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 업데이트할 수도 있습니다.

![Azure Files 조건부 헤더에 대한 스토리지 탐색기 콘텐츠 캐시 수정](media/storage-files-condition-headers/storage-explorer-cache.png)