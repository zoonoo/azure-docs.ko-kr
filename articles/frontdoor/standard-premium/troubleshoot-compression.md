---
title: Azure Front Door 표준/프리미엄에서 파일 압축 문제 해결
description: Azure Front Door에서 파일 압축 문제를 해결하는 방법을 알아봅니다. 이 문서에서는 몇 가지 가능한 원인을 설명합니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2020
ms.author: qixwang
ms.openlocfilehash: c912ebde5499ec2f9f68e5f5762af60823faefe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100233"
---
# <a name="troubleshooting-azure-front-door-standardpremium-file-compression"></a>Azure Front Door 표준/프리미엄 파일 압축 문제 해결

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

이 문서는 Azure Front Door 표준/프리미엄 파일 압축 문제를 해결하는 데 도움이 됩니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="symptom"></a>증상

경로에 대한 압축이 활성화되어 있지만 파일이 압축되지 않은 상태로 반환됩니다.

> [!TIP]
> 반환된 파일이 압축되었는지 확인하려면 [Fiddler](https://www.telerik.com/fiddler) 또는 브라우저의 [개발자 도구](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)와 같은 도구를 사용해야 합니다.  캐시된 CDN 콘텐츠와 함께 반환된 HTTP 응답 헤더를 확인하세요.  명명된 `Content-Encoding` 헤더에 **gzip**, **bzip2** 또는 **deflate** 값이 있는 경우 콘텐츠가 압축된 것입니다.
> 
> ![Content-Encoding 헤더](../media/troubleshoot-compression/content-header.png)
> 

## <a name="cause"></a>원인

몇 가지 가능한 원인은 다음과 같습니다.

* 요청된 콘텐츠에 압축을 사용할 수 없습니다.
* 요청된 파일 형식에 대해 압축이 활성화되지 않았습니다.
* HTTP 요청에 유효한 압축 형식을 요청하는 헤더가 포함되지 않았습니다.
* 원본에서 청크 분할된 콘텐츠를 보내는 중입니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계

> [!TIP]
> 새 엔드포인트 배포와 마찬가지로, AFD 구성 변경이 네트워크 전체에 전파되려면 다소 시간이 걸립니다.  일반적으로 변경 내용은 90분 이내에 적용됩니다.  CDN 엔드포인트에 압축을 처음으로 설정한 경우 압축 설정이 POP까지 전파되도록 1~2시간 기다리는 것이 좋습니다. 
> 

### <a name="verify-the-request"></a>요청 확인

먼저 요청을 다시 확인해야 합니다. 브라우저의 **[개발자 도구](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)** 를 사용하여 생성되는 요청을 볼 수 있습니다.

* 요청이 원본이 아닌 엔드포인트 URL `<endpointname>.z01.azurefd.net`에 전송되는지 확인합니다.
* 요청에 **Accept-Encoding** 헤더가 포함되는지, 헤더 값에 **gzip**, **deflate** 또는 **bzip2** 가 포함되는지 확인합니다.

![CDN 요청 헤더](../media/troubleshoot-compression/request-headers.png)

### <a name="verify-compression-settings"></a>압축 설정 확인

[Azure Portal](https://portal.azure.com)에서 엔드포인트로 이동하고 경로 패널에서 **구성** 단추를 선택합니다. 압축이 **활성화** 되어 있는지 확인합니다.

![CDN 압축 설정](../media/troubleshoot-compression/compression-settings.png)

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>원본 서버의 요청에서 **Via** 헤더 확인

**Via** HTTP 헤더는 요청이 프록시 서버에 의해 전달되고 있음을 웹 서버에 알립니다.  기본적으로 Microsoft IIS 웹 서버는 요청에 **Via** 헤더가 들어 있으면 응답을 압축하지 않습니다.  이 동작을 재정의하려면 다음을 수행합니다.

* **IIS 6**: IIS 메타베이스 속성에서 HcNoCompressionForProxies="FALSE"를 설정합니다. 자세한 내용은 [IIS 6 압축](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))을 참조하세요.
* **IIS 7 이상**: 서버 구성에서 **noCompressionForHttp10** 및 **noCompressionForProxies** 를 모두 *False* 로 설정합니다. 자세한 내용은 [HTTP 압축](https://www.iis.net/configreference/system.webserver/httpcompression)을 참조하세요.
