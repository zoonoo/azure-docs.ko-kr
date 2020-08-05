---
title: UX Azure Data Factory 문제 해결
description: Azure Data Factory UX 문제를 해결 하는 방법을 알아봅니다.
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9a96c385c25eea1c5e217665f162de402a73e558
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567951"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Azure Data Factory UX 문제 해결
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory UX에 대 한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="adf-ux-not-loading"></a>ADF UX 로드 안 함

> [!NOTE]
> Azure Data Factory UX는 Microsoft Edge 및 Google Chrome을 공식적으로 지원 합니다. 다른 웹 브라우저를 사용 하면 예기치 않은 동작이 나 문서화 되지 않은 동작이 발생할 수 있습니다.

### <a name="third-party-cookies-blocked"></a>차단 된 타사 쿠키

ADF UX는 브라우저 쿠키를 사용 하 여 사용자 세션을 유지 하 고 대화형 개발 및 모니터링 환경을 가능 하 게 합니다. Incognito 세션을 사용 하 고 있거나 ad 차단을 사용 하도록 설정 했기 때문에 브라우저에서 타사 쿠키를 차단할 수 있습니다. 타사 쿠키를 차단 하면 빈 페이지로 리디렉션되는 등의 방법으로 포털을 로드 https://adf.azure.com/accesstoken.html 하거나 타사 쿠키가 차단 되었다는 경고 메시지를 받을 때 문제가 발생할 수 있습니다. 이 문제를 해결 하려면 다음 단계를 사용 하 여 브라우저에서 타사 쿠키 옵션을 사용 하도록 설정 합니다.

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>모든 쿠키 허용

1. 브라우저에서 **chrome://settings/cookies** 을 방문 합니다.
1. **모든 쿠키 허용** 옵션을 선택 합니다. ![](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. ADF UX를 새로 고치고 다시 시도 하세요.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>ADF UX만 쿠키를 사용할 수 있도록 허용
모든 쿠키를 허용 하지 않으려는 경우 ADF UX를 선택적으로 허용할 수 있습니다.
1. **Chrome://settings/cookies**을 방문 하세요.
1. **항상 쿠키를 사용할 수 있는 사이트** 옵션 **을 클릭** 하 여 ![ 허용 된 사이트에 ADF UX 추가를 클릭 합니다.](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. **Adf.azure.com** 사이트를 추가 하 고 **모든 쿠키** 옵션을 선택한 다음 저장을 선택 합니다. ![ADF UX 사이트의 모든 쿠키 허용](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. ADF UX를 새로 고치고 다시 시도 하세요.


### <a name="microsoft-edge"></a>Microsoft Edge

1. 브라우저에서 **edge://settings/content/cookies** 을 방문 합니다.
1. **사이트에서 쿠키 데이터를 저장 하 고 읽을 수 있음** 을 사용 하도록 설정 하 고 **타사 쿠키** 옵션을 사용 하지 않도록 설정 하 여 ![ Edge의 모든 쿠키 허용을 사용 하지 않도록 설정](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. ADF UX를 새로 고치고 다시 시도 하세요.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>ADF UX만 쿠키를 사용할 수 있도록 허용

모든 쿠키를 허용 하지 않으려는 경우 ADF UX를 선택적으로 허용할 수 있습니다.

1. **Edge://settings/content/cookies**을 방문 하세요.
1. **허용** 섹션에서 **추가** 를 클릭 하 고 **adf.azure.com** site를 추가 합니다. ![허용 된 사이트에 ADF UX 추가](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. ADF UX를 새로 고치고 다시 시도 하세요.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

* [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
* [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
* [Azure 비디오](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
