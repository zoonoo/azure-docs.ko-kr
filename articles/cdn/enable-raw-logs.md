---
title: Azure CDN HTTP 원시 로그
description: 이 문서에서는 Azure CDN HTTP 원시 로그를 설명 합니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80371629"
---
# <a name="azure-cdn-http-raw-logs"></a>Azure CDN HTTP 원시 로그
원시 로그는 감사 및 문제 해결에 중요 한 작업 및 오류에 대 한 다양 한 정보를 제공 합니다. 원시 로그는 활동 로그와 다릅니다. 활동 로그는 Azure 리소스에서 수행 된 작업에 대 한 가시성을 제공 합니다. 원시 로그는 리소스 작업에 대 한 레코드를 제공 합니다.

> [!IMPORTANT]
> HTTP 원시 로그 기능은 Microsoft의 Azure CDN 사용할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="configuration"></a>Configuration

Microsoft 프로필에서 Azure CDN에 대 한 원시 로그를 구성 하려면: 

1. Azure Portal 메뉴에서 **모든 리소스** >> **\<(CDN 프로필>** 를 선택 합니다.

2. **모니터링**아래에서 **진단 설정**을 선택 합니다.

3. **+ 진단 설정 추가**를 선택 합니다.

    ![CDN 진단 설정](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > 원시 로그는 집계 된 http 상태 코드 로그를 끝점 수준에서 사용할 수 있는 동안 프로필 수준 에서만 사용할 수 있습니다.

4. 진단 **설정 아래에서**진단 **설정 이름**아래에 진단 설정의 이름을 입력 합니다.

5. **로그** 를 선택 하 고 보존 기간 (일)을 설정 합니다.

6. **대상 세부 정보**를 선택 합니다. 대상 옵션은 다음과 같습니다.
    * **Log Analytics에 보내기**
        * **구독** 및 **Log Analytics 작업 영역**을 선택 합니다.
    * **저장소 계정에 보관**
        * **구독** 및 **저장소 계정을**선택 합니다.
    * **이벤트 허브로 스트림**
        * **구독**, **이벤트 허브 네임 스페이스**, **이벤트 허브 이름 (선택 사항)** 및 **이벤트 허브 정책 이름을**선택 합니다.

    ![CDN 진단 설정](./media/cdn-raw-logs/raw-logs-02.png)

7. **저장**을 선택합니다.

## <a name="raw-logs-properties"></a>원시 로그 속성

Microsoft 서비스의 Azure CDN는 현재 원시 로그를 제공 합니다. 원시 로그는 각 항목이 다음 스키마를 갖는 개별 API 요청을 제공 합니다. 

| 속성              | Description                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | 프런트 도어에서 제공 하는 요청을 식별 하는 고유한 참조 문자열로, 클라이언트에 대 한 X-y 헤더로도 전송 됩니다. 특정 요청에 대 한 액세스 로그에서 세부 정보를 검색 하는 데 필요 합니다. |
| HttpMethod            | 요청에서 사용된 HTTP 메서드                                                                                                                                                                     |
| HttpVersion           | 요청 또는 연결의 유형입니다.                                                                                                                                                                   |
| RequestUri            | 받은 요청의 URI                                                                                                                                                                         |
| RequestBytes          | 요청 헤더 및 요청 본문을 포함 하 여 HTTP 요청 메시지의 크기 (바이트)입니다.                                                                                                   |
| ResponseBytes         | 백 엔드 서버에서 응답으로 보낸 바이트 수입니다.                                                                                                                                                    |
| UserAgent             | 클라이언트에서 사용 하는 브라우저 유형입니다.                                                                                                                                                               |
| ClientIp              | 요청한 클라이언트의 IP 주소입니다.                                                                                                                                                  |
| TimeTaken             | 작업에 걸린 시간 (밀리초)입니다.                                                                                                                                            |
| SecurityProtocol      | 요청에 사용 되는 TLS/SSL 프로토콜 버전 이거나, 암호화가 없는 경우 null입니다.                                                                                                                           |
| 엔드포인트              | CDN 끝점 호스트가 부모 CDN 프로필 아래에서 구성 되었습니다.                                                                                                                                   |
| 백 엔드 호스트 이름     | 요청이 전송 되는 백 엔드 호스트 또는 원본 이름입니다.                                                                                                                                |
| 원본 방패로 전송 | True 이면 요청이에 지 pop 대신 원본 방패 캐시에서 응답 했음을 의미 합니다. 원본 방패는 캐시 적중률을 향상 시키는 데 사용 되는 부모 캐시입니다.                                       |
| HttpStatusCode        | 프록시에서 반환 된 HTTP 상태 코드입니다.                                                                                                                                                        |
| HttpStatusDetails     | 요청에 대 한 결과 상태입니다. 이 문자열 값의 의미는 상태 참조 테이블에서 찾을 수 있습니다.                                                                                              |
| 창을                   | 사용자 요청에 응답 하는에 지 pop입니다. Pop의 약어는 해당 metros의 공항 코드입니다.                                                                                   |
| 캐시 상태          | 개체가 캐시에서 반환 되었는지 아니면 원본에서 제공 되었는지를 나타냅니다.                                                                                                             |
> [!IMPORTANT]
> HTTP 원시 로그 기능은 **2020 년 2 월 25**일 이후에 만들어지거나 업데이트 된 모든 프로필에 대해 자동으로 사용할 수 있습니다. 이전에 만든 CDN 프로필의 경우 로깅을 설정한 후 CDN 끝점을 업데이트 해야 합니다. 예를 들어 CDN 끝점에서 지역 필터링으로 이동 하 여 해당 워크 로드와 관련이 없는 국가를 차단 하 고 저장을 누를 수 있습니다. 

> [!NOTE]
> 쿼리를 실행 하 여 Log Analytics 프로필에서 로그를 볼 수 있습니다. 예제 쿼리는 AzureDiagnostics 처럼 보입니다. where Category = = "AzureCdnAccessLog"

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft CDN 서비스에 대 한 HTTP 원시 로그를 사용 하도록 설정 했습니다.

이 문서에 언급 된 Azure CDN 및 기타 Azure 서비스에 대 한 자세한 내용은 다음을 참조 하세요.

* [분석](cdn-log-analysis.md) 사용 패턴을 Azure CDN 합니다.

* [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)에 대해 자세히 알아보세요.

* [Azure Monitor에서 Log Analytics를](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)구성 합니다.
