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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "70737494"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>오류 조건헤더브라우저에서 Azure 파일을 사용하는 웹 응용 프로그램에서 지원되지 않음

조건헤더NotSupported 오류는 웹 브라우저와 같은 조건부 헤더를 사용하는 응용 프로그램을 통해 Azure Files에서 호스팅되는 콘텐츠에 액세스할 때 발생하며 액세스가 실패합니다. 오류 상태 조건 헤더는 지원 되지 않습니다.

![Azure 파일 조건부 헤더 오류](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>원인

조건부 헤더는 아직 지원되지 않습니다. 이를 구현하는 응용 프로그램은 파일에 액세스할 때마다 전체 파일을 요청해야 합니다.

### <a name="workaround"></a>해결 방법

새 파일이 업로드되면 기본적으로 캐시 제어 속성은 "캐시 없음"입니다. 응용 프로그램이 매번 파일을 요청하도록 하려면 파일의 캐시 제어 속성을 "캐시 없음"에서 "캐시 없음, 저장소 없음, 유효성 검사"로 업데이트해야 합니다. Azure [저장소 탐색기를](https://azure.microsoft.com/features/storage-explorer/)사용하여 이 작업을 수행할 수 있습니다.

![Azure 파일 조건부 헤더에 대한 저장소 탐색기 콘텐츠 캐시 수정](media/storage-files-condition-headers/storage-explorer-cache.png)