---
title: Azure 전면 도어 표준/프리미엄에서 파일 압축 문제 해결
description: Azure Front 도어에서 파일 압축 문제를 해결 하는 방법에 대해 알아봅니다. 이 문서에서는 몇 가지 가능한 원인을 설명 합니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2020
ms.author: qixwang
ms.openlocfilehash: c912ebde5499ec2f9f68e5f5762af60823faefe1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100233"
---
# <a name="troubleshooting-azure-front-door-standardpremium-file-compression"></a>Azure Front 도어 표준/프리미엄 파일 압축 문제 해결

> [!Note]
> 이 설명서는 Azure Front 도어 Standard/Premium (미리 보기)에 대 한 것입니다. Azure Front 문에 대 한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

이 문서는 Azure Front 도어 표준/프리미엄 파일 압축 문제를 해결 하는 데 도움이 됩니다.

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="symptom"></a>증상

경로에 대 한 압축을 사용할 수 있지만 파일이 압축 되지 않은 상태로 반환 됩니다.

> [!TIP]
> 반환된 파일이 압축되었는지 확인하려면 [Fiddler](https://www.telerik.com/fiddler) 또는 브라우저의 [개발자 도구](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)와 같은 도구를 사용해야 합니다.  캐시된 CDN 콘텐츠와 함께 반환된 HTTP 응답 헤더를 확인하세요.  명명된 `Content-Encoding` 헤더에 **gzip**, **bzip2** 또는 **deflate** 값이 있는 경우 콘텐츠가 압축된 것입니다.
> 
> ![Content-Encoding 헤더](../media/troubleshoot-compression/content-header.png)
> 

## <a name="cause"></a>원인

몇 가지 가능한 원인은 다음과 같습니다.

* 요청한 콘텐츠는 압축에 적합 하지 않습니다.
* 요청한 파일 형식에 압축을 사용할 수 없습니다.
* HTTP 요청에 유효한 압축 형식을 요청 하는 헤더가 포함 되어 있지 않습니다.
* 원본에서 청크 분할 된 콘텐츠를 보내는 중입니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계

> [!TIP]
> 새 끝점을 배포 하는 것 처럼, AFD 구성 변경은 네트워크를 통해 전파 되는 데 약간의 시간이 걸립니다.  일반적으로 변경 내용은 90분 이내에 적용됩니다.  CDN 엔드포인트에 압축을 처음으로 설정한 경우 압축 설정이 POP까지 전파되도록 1~2시간 기다리는 것이 좋습니다. 
> 

### <a name="verify-the-request"></a>요청 확인

먼저 요청을 다시 확인 해야 합니다. 브라우저의 **[개발자 도구](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)** 를 사용 하 여 생성 되는 요청을 볼 수 있습니다.

* 요청이 원본이 아닌 끝점 URL, 및에 전송 되 고 있는지 확인 합니다 `<endpointname>.z01.azurefd.net` .
* 요청에 **Accept-Encoding** 헤더가 포함되는지, 헤더 값에 **gzip**, **deflate** 또는 **bzip2** 가 포함되는지 확인합니다.

![CDN 요청 헤더](../media/troubleshoot-compression/request-headers.png)

### <a name="verify-compression-settings"></a>압축 설정 확인

[Azure Portal](https://portal.azure.com) 에서 끝점으로 이동 하 여 경로 패널에서 **구성** 단추를 선택 합니다. 압축이 **사용** 되는지 확인 합니다.

![CDN 압축 설정](../media/troubleshoot-compression/compression-settings.png)

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>원본 서버의 요청에서 **Via** 헤더 확인

**Via** HTTP 헤더는 요청이 프록시 서버에 의해 전달되고 있음을 웹 서버에 알립니다.  Microsoft IIS 웹 서버는 요청에 **Via** 헤더가 포함 된 경우 기본적으로 응답을 압축 하지 않습니다.  이 동작을 재정의 하려면 다음을 수행 합니다.

* **Iis 6**: Iis 메타 베이스 속성에서 HcNoCompressionForProxies = "FALSE"를 설정 합니다. 자세한 내용은 [IIS 6 압축](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))을 참조 하세요.
* **IIS 7 이상**: 서버 구성에서 **noCompressionForHttp10** 및 **noCompressionForProxies** 를 모두 *False* 로 설정 합니다. 자세한 내용은 [HTTP 압축](https://www.iis.net/configreference/system.webserver/httpcompression)을 참조 하세요.
