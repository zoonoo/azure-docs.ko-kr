---
title: Azure CDN HTTP 원시 로그
description: 이 문서에서는 Azure CDN HTTP 원시 로그에 대해 설명합니다.
services: cdn
author: sohamnchatterjee
manager: danielgi
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2020
ms.author: sohamnc
ms.openlocfilehash: c6e8570746ae3dd0051dbec084c89d90580d28b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371629"
---
# <a name="azure-cdn-http-raw-logs"></a>Azure CDN HTTP 원시 로그
Raw 로그는 감사 및 문제 해결에 중요한 작업 및 오류에 대한 풍부한 정보를 제공합니다. 원시 로그는 활동 로그와 다릅니다. 활동 로그는 Azure 리소스에서 수행된 작업에 대한 가시성을 제공합니다. 원시 로그는 리소스의 작업 레코드를 제공합니다.

> [!IMPORTANT]
> HTTP 원시 로그 기능은 Microsoft의 Azure CDN에서 사용할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

에서 [https://portal.azure.com](https://portal.azure.com)Azure 포털에 로그인합니다.

## <a name="configuration"></a>Configuration

Microsoft 프로필에서 Azure CDN에 대한 원시 로그를 구성하려면 다음을 수행하십시오. 

1. Azure 포털 메뉴에서 CDN 프로필>**모든 리소스를** >> **\<선택합니다. **

2. **모니터링에서** **진단 설정을 선택합니다.**

3. **+ 진단 설정 추가를**선택합니다.

    ![CDN 진단 설정](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > 원시 로그는 프로필 수준에서만 사용할 수 있으며 집계된 http 상태 코드 로그는 끝점 수준에서 사용할 수 있습니다.

4. **진단 설정에서**진단 설정 이름 아래에 진단 **설정의**이름을 입력합니다.

5. **로그를** 선택하고 보존을 일 수로 설정합니다.

6. 대상 **세부 정보를 선택합니다.** 대상 옵션은 다음과 같습니다.
    * **Log Analytics에 보내기**
        * **구독** 및 **로그 분석 작업 영역을**선택합니다.
    * **저장소 계정에 보관**
        * **구독** 및 **저장소 계정을**선택합니다.
    * **이벤트 허브로 스트리밍**
        * **구독,** **이벤트 허브 네임스페이스,** **이벤트 허브 이름(선택 사항)** 및 **이벤트 허브 정책 이름을**선택합니다.

    ![CDN 진단 설정](./media/cdn-raw-logs/raw-logs-02.png)

7. **저장**을 선택합니다.

## <a name="raw-logs-properties"></a>원시 로그 속성

Microsoft 서비스의 Azure CDN은 현재 원시 로그를 제공합니다. 원시 로그는 다음 스키마를 갖는 각 항목에 개별 API 요청을 제공합니다. 

| 속성              | 설명                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 추적 참조     | 정문에서 제공하는 요청을 식별하는 고유한 참조 문자열로 X-Azure-Ref 헤더로 클라이언트에 전송됩니다. 특정 요청에 대한 액세스 로그에서 세부 정보를 검색하는 데 필요합니다. |
| HttpMethod            | 요청에서 사용된 HTTP 메서드                                                                                                                                                                     |
| HttpVersion           | 요청 또는 연결의 유형입니다.                                                                                                                                                                   |
| 요청우리            | 받은 요청의 URI                                                                                                                                                                         |
| 요청 바이트          | 요청 헤더 및 요청 본문을 포함하여 바이트내의 HTTP 요청 메시지 크기입니다.                                                                                                   |
| 응답바이트         | 백 엔드 서버에서 응답으로 전송하는 바이트입니다.                                                                                                                                                    |
| UserAgent             | 클라이언트가 사용한 브라우저 유형입니다.                                                                                                                                                               |
| ClientIp              | 요청한 클라이언트의 IP 주소입니다.                                                                                                                                                  |
| TimeTaken             | 작업이 걸린 시간(밀리초)입니다.                                                                                                                                            |
| 보안 프로토콜      | 암호화가 없는 경우 요청 또는 null에서 사용하는 TLS/SSL 프로토콜 버전입니다.                                                                                                                           |
| 엔드포인트              | CDN 끝점 호스트가 상위 CDN 프로필로 구성되었습니다.                                                                                                                                   |
| 백 엔드 호스트 이름     | 요청이 전송되는 백 엔드 호스트 또는 원본의 이름입니다.                                                                                                                                |
| 원원 쉴드로 전송 | true이면 에지 팝 대신 원본 쉴드 캐시에서 요청에 응답했다는 의미입니다. Origin 쉴드는 캐시 적중 률을 개선하는 데 사용되는 상위 캐시입니다.                                       |
| HttpStatusCode        | 프록시에서 반환된 HTTP 상태 코드입니다.                                                                                                                                                        |
| Http상태 세부 정보     | 요청의 결과 상태입니다. 이 문자열 값의 의미는 상태 참조 테이블에서 찾을 수 있습니다.                                                                                              |
| 팝                   | 사용자 요청에 응답한 에지 팝입니다. POP의 약어는 해당 지하철의 공항 코드입니다.                                                                                   |
| 캐시 상태          | 개체가 캐시에서 반환되었는지 또는 원본에서 왔는지 나타냅니다.                                                                                                             |
> [!IMPORTANT]
> HTTP Raw 로그 기능은 **2020년 2월 25일**이후에 생성하거나 업데이트된 모든 프로필에 대해 자동으로 사용할 수 있습니다. 이전에 만든 CDN 프로필의 경우 로깅을 설정한 후 CDN 끝점을 업데이트해야 합니다. 예를 들어 CDN 끝점에서 지리적 필터링으로 이동하여 워크로드와 관련이 없는 국가를 차단하고 저장을 누를 수 있습니다. 

> [!NOTE]
> 로그는 쿼리를 실행하여 Log Analytics 프로필 에서 볼 수 있습니다. 샘플 쿼리는 AzureDiagnostics | 여기서 범주 == "AzureCdnAccessLog"

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft CDN 서비스에 대한 HTTP 원시 로그를 사용하도록 설정했습니다.

이 문서에서 언급한 Azure CDN 및 기타 Azure 서비스에 대한 자세한 내용은 다음을 참조하십시오.

* [분석](cdn-log-analysis.md) Azure CDN 사용 패턴입니다.

* [Azure 모니터에](https://docs.microsoft.com/azure/azure-monitor/overview)대해 자세히 알아보기.

* [Azure 모니터에서 로그 분석을 구성합니다.](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
