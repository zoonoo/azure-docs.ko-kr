<properties
	pageTitle="CDN - 파일 압축 문제 해결"
	description="CDN 파일 압축 관련 문제 해결"
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
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

> [AZURE.TIP] 새 끝점 배포와 마찬가지로, CDN 구성 변경이 네트워크 전체에 전파되려면 다소 시간이 걸립니다. 대부분의 경우 90분 내에 변경 내용이 적용됩니다. CDN 끝점에 압축을 처음으로 설정한 경우 압축 설정이 POP까지 전파되도록 1~2시간 기다리는 것이 좋습니다.

### 요청 확인

우선, 요청에 대해 빠른 온전성 검사를 수행합니다. 브라우저의 [개발자 도구](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)를 사용하여 생성되는 요청을 볼 수 있습니다.

- 요청이 원본이 아닌 끝점 URL, `<endpointname>.azureedge.net`에 전송되는지 확인합니다.
- 요청에 **Accept-Encoding** 헤더가 포함되는지, 헤더 값에 **gzip**, **deflate** 또는 **bzip2**가 포함되는지 확인합니다.

> [AZURE.NOTE] **Akamai의 Azure CDN** 프로필은 **gzip** 인코딩만 지원합니다.

![CDN 요청 헤더](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### 압축 설정 확인(Standard CDN 프로필)

> [AZURE.NOTE] 이 단계는 CDN 프로필이 **Verizon의 Azure CDN Standard** 또는 **Akamai의 Azure CDN Standard** 프로필인 경우에만 적용됩니다.

[Azure 포털](https://portal.azure.com)에서 끝점으로 이동하여 **구성** 단추를 클릭합니다.

- 압축이 활성화되어 있는지 확인합니다.
- 압축될 콘텐츠에 대한 MIME 형식이 압축된 형식의 목록에 포함되어 있는지 확인합니다.

![CDN 압축 설정](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### 압축 설정 확인(Premium CDN 프로필)

> [AZURE.NOTE] 이 단계는 CDN 프로필이 **Verizon의 Azure CDN Premium** 프로필인 경우에만 적용됩니다.

[Azure 포털](https://portal.azure.com)에서 끝점으로 이동하여 **관리** 단추를 클릭합니다. 보조 포털이 열립니다. **HTTP Large**(HTTP 크게) 탭을 가리킨 다음 **캐시 설정** 플라이아웃을 가리킵니다. **압축**을 클릭합니다.

- 압축이 활성화되어 있는지 확인합니다.
- **파일 형식** 목록에 쉼표로 구분된(공백 없음) MIME 형식 목록이 포함되는지 확인합니다.
- 압축될 콘텐츠에 대한 MIME 형식이 압축된 형식의 목록에 포함되어 있는지 확인합니다.

![CDN 프리미엄 압축 설정](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### 콘텐츠 캐시 여부 확인

> [AZURE.NOTE] 이 단계는 CDN 프로필이 **Verizon의 Azure CDN** 프로필(Standard 또는 Premium)인 경우에만 적용됩니다.

브라우저의 개발자 도구를 사용하여, 요청되는 지역에서 파일이 캐시되는지 응답 헤더를 확인합니다.

- **Server** 응답 헤더를 확인합니다. 헤더에는 **Platform (POP/Server ID)** 형식이 포함되어야 합니다(아래 예제 참조).
- **X-Cache** 응답 헤더를 확인합니다. 헤더는 **HIT**여야 합니다.

![CDN 응답 헤더](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### 파일의 크기 요건 충족 여부 확인

> [AZURE.NOTE] 이 단계는 CDN 프로필이 **Verizon의 Azure CDN** 프로필(Standard 또는 Premium)인 경우에만 적용됩니다.

압축을 적용할 수 있으려면, 파일은 다음과 같은 크기 요건을 충족해야 합니다.

- 128바이트 초과.
- 1MB 미만.

### 원본 서버의 요청에서 **Via** 헤더 확인

**Via** HTTP 헤더는 요청이 프록시 서버에 의해 전달되고 있음을 웹 서버에 알립니다. 기본적으로 Microsoft IIS 웹 서버는 요청에 **Via** 헤더가 들어 있으면 응답을 압축하지 않습니다. 이 동작을 재정의하려면 다음 단계를 수행합니다.

- **IIS 6**: [IIS 메타베이스 속성에서 HcNoCompressionForProxies="FALSE" 설정](https://msdn.microsoft.com/library/ms525390.aspx)
- **IIS 7 이상**: [서버 구성에서 **noCompressionForHttp10** 및 **noCompressionForProxies**를 둘 다 False로 설정](http://www.iis.net/configreference/system.webserver/httpcompression)

<!---HONumber=AcomDC_0720_2016-->