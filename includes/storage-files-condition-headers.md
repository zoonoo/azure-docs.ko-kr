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
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70737494"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>브라우저의 Azure Files를 사용 하 여 웹 응용 프로그램에서 오류 ConditionHeadersNotSupported

ConditionHeadersNotSupported 오류는 웹 브라우저와 같은 조건부 헤더를 사용 하는 응용 프로그램을 통해 Azure Files에서 호스팅되는 콘텐츠에 액세스할 수 없는 경우에 발생 합니다. 오류는 조건 헤더가 지원 되지 않는다는 것을 알려 주는 것입니다.

![조건부 헤더 Azure Files 오류](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>원인

조건부 헤더는 아직 지원 되지 않습니다. 이를 구현 하는 응용 프로그램은 파일에 액세스할 때마다 전체 파일을 요청 해야 합니다.

### <a name="workaround"></a>해결 방법

새 파일이 업로드 되 면 기본적으로 cache-control 속성은 "cache no"입니다. 응용 프로그램이 매번 파일을 요청 하도록 강제 하려면 파일의 cache-control 속성을 "캐시 없음"에서 "캐시 없음, 저장소 없음, 유효성 재검사로 업데이트 해야 합니다."로 업데이트 해야 합니다. 이는 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)을 사용 하 여 수행할 수 있습니다.

![Azure Files 조건부 헤더에 대 한 저장소 탐색기 콘텐츠 캐시 수정](media/storage-files-condition-headers/storage-explorer-cache.png)