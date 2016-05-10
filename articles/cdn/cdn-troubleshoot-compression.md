<properties
	pageTitle="CDN - 파일 압축 문제 해결"
	description="CDN 파일 압축 관련 문제 해결"
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/21/2016" 
	ms.author="casoper"/>
    
# CDN 파일 압축 문제 해결

이 문서는 [CDN 파일 압축](cdn-improve-performance.md) 관련 문제를 해결하는 데 도움이 됩니다.

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수도 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동한 다음 **지원 받기**를 클릭합니다.

## 증상

끝점에 대한 압축이 활성화되어 있지만 파일이 압축되지 않은 상태로 반환됩니다.

## 원인

몇 가지 가능한 원인은 다음과 같습니다.

- 요청된 콘텐츠에 압축을 사용할 수 없습니다.
- 압축은 요청된 파일 형식에 대해 활성화되지 않습니다.
- HTTP 요청에 유효한 압축 형식을 요청하는 헤더가 포함되지 않았습니다.

## 문제 해결 단계

### 요청 확인

우선, 요청에 대해 빠른 온전성 검사를 수행합니다. 브라우저의 [개발자 도구](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)를 사용하여 생성되는 요청을 볼 수 있습니다.

- 요청이 원본이 아닌 끝점 URL, `<endpointname>.azureedge.net`에 전송되는지 확인합니다.
- 요청에 **Accept-Encoding** 헤더가 포함되는지, 헤더 값에 **gzip**, **defalte**, 또는 **bzip2**가 포함되는지 확인합니다.

![CDN 요청 헤더](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### 압축 설정 확인(Standard CDN 프로필)

> [AZURE.NOTE] 이 단계는 CDN 프로필이 **표준** 가격 책정 계층인 경우에만 적용됩니다.

[Azure 포털](https://portal.azure.com)에서 끝점으로 이동하여 **구성** 단추를 클릭합니다.

- 압축이 활성화되어 있는지 확인합니다.
- 압축될 콘텐츠에 대한 MIME 형식이 압축된 형식의 목록에 포함되어 있는지 확인합니다.

![CDN 압축 설정](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### 압축 설정 확인(Premium CDN 프로필)

> [AZURE.NOTE] 이 단계는 CDN 프로필이 **프리미엄** 가격 책정 계층인 경우에만 적용됩니다.

[Azure 포털](https://portal.azure.com)에서 끝점으로 이동하여 **관리** 단추를 클릭합니다. 보조 포털이 열립니다. **HTTP Large**(HTTP 크게) 탭을 가리킨 다음 **캐시 설정** 플라이아웃을 가리킵니다. **압축**을 클릭합니다.

- 압축이 활성화되어 있는지 확인합니다.
- **파일 형식** 목록에 쉼표로 구분된 MIME 형식 목록이 포함되는지 확인합니다.
- 압축될 콘텐츠에 대한 MIME 형식이 압축된 형식의 목록에 포함되어 있는지 확인합니다.

![CDN 프리미엄 압축 설정](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)


### 콘텐츠 캐시 여부 확인

브라우저의 개발자 도구를 사용하여, 요청되는 지역에서 파일이 캐시되는지 응답 헤더를 확인합니다.

- **서버** 응답 헤더를 확인합니다. 헤더에는 **Platform (POP/Server ID)** 형식이 포함되어야 합니다(아래 예제 참조).
- **X-Cache** 응답 헤더를 확인합니다. 헤더는 **HIT**여야 합니다.  

![CDN 응답 헤더](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### 파일의 크기 요건 충족 여부 확인

압축을 적용할 수 있으려면, 파일은 다음과 같은 크기 요건을 충족해야 합니다.

- 128바이트 초과.
- 1MB 미만.

<!---HONumber=AcomDC_0427_2016-->