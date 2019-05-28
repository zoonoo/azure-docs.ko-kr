---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/17/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0486b595bffd18b06d54e8377b24deab04e2aa93
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159860"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>브라우저에서 Azure Files를 사용 하 여 웹 응용 프로그램에서 오류 ConditionHeadersNotSupported

웹 브라우저 등의 조건부 헤더를 사용 하는 응용 프로그램을 통해 Azure Files에서 호스트 되는 콘텐츠에 액세스 하는 경우 액세스 실패 ConditionHeadersNotSupported 오류를 표시 합니다.

![ConditionHeaderNotSupported 오류](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>원인

조건부 헤더를 아직 지원 되지 않습니다. 스크립트를 구현 하는 응용 프로그램을 파일에 액세스할 때마다 전체 파일을 요청 해야 합니다.

### <a name="workaround"></a>해결 방법

새 파일이 업로드 되 면 기본적으로 캐시 제어 속성은 "no-cache". 응용 프로그램에서 파일을 요청 될 때마다 파일의 캐시 제어 속성 업데이트 해야 "no-cache"에서 "no cache, 없음-저장소에 있어야 합니다-재검사"를 합니다. 사용 하 여 수행할 수 있습니다 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)합니다.

![저장소 탐색기 콘텐츠 캐시가 수정](media/storage-files-condition-headers/storage-explorer-cache.png)