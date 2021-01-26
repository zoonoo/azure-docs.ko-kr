---
title: REST API를 사용 하 여 Azure 센티널에서 watchlists 관리 | Microsoft Docs
description: 이 문서에서는 watchlists 및 해당 항목을 만들고, 수정 하 고, 삭제 하기 위해 Log Analytics ' REST API를 사용 하 여 Azure 센티널 watchlists를 관리 하는 방법을 설명 합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2021
ms.author: yelevin
ms.openlocfilehash: ea571f9b033ba82709a13c6d32649f3228ee04b1
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798464"
---
# <a name="manage-watchlists-in-azure-sentinel-using-rest-api"></a>REST API를 사용 하 여 Azure 센티널에서 watchlists 관리

> [!IMPORTANT]
> Watchlists 기능은 현재 **미리 보기** 상태입니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

Azure Monitor Log Analytics에 포함 되는 Azure 센티널은 Log Analytics ' REST API를 사용 하 여 watchlists를 관리할 수 있습니다. 이 문서에서는 REST API를 사용 하 여 watchlists 및 해당 항목을 만들고 수정 하 고 삭제 하는 방법을 보여 줍니다.  이러한 방식으로 만들어진 Watchlists는 Azure 센티널 UI에 표시 됩니다.

## <a name="common-uri-parameters"></a>공통 URI 매개 변수

다음은 모든 관심 목록 API 명령에 대 한 일반적인 URI 매개 변수입니다.

| 이름 | In(다음 안에) | 필수 | Type | 설명 |
|-|-|-|-|-|
| **구독** | 경로 | yes | GUID | Azure 구독 ID |
| **ResourceGroupName** | 경로 | yes | 문자열 | 구독 내 리소스 그룹의 이름 |
| **WorkspaceName** | 경로 | yes | 문자열 | Log Analytics 작업 영역의 이름입니다. |
| **{watchlistAlias}** | 경로 | 예* | 문자열 | 지정 된 관심 목록의 이름입니다.<br>\* 모든 watchlists를 검색할 때 필요 하지 않음 |
| **{watchlistItemId}** | 경로 | 예 * * | GUID | 관심 목록에서 만들거나, 추가 하거나, 삭제할 항목의 ID입니다.<br>\*\* 관심 목록 item 명령에만 필요 합니다. |
| **{api-version}** | Query | yes | 문자열 | 이 요청을 수행 하는 데 사용 되는 프로토콜의 버전입니다. 2020 년 10 월 29 일, 관심 목록 API 버전은 *2019-01-01-미리 보기* 입니다. |
| **{bearerToken}** | 권한 부여 | yes | token | 전달자 권한 부여 토큰 |
|  

## <a name="retrieve-all-watchlists"></a>모든 watchlists 검색

이 명령은 해당 항목 없이 작업 영역과 연결 된 모든 watchlists를 검색 합니다.

### <a name="request-uri"></a>요청 URI
(URI는 한 줄 이며 가독성을 높이기 위해 분리 됨)

| 방법 | 요청 URI |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists?api-version={{api-version}}` |
|

### <a name="responses"></a>응답

| 상태 코드 | 응답 본문 | 설명 |
|-|-|-|
| 200/양호 | 기존 watchlists의 목록입니다. 관심 목록을 찾을 수 없는 경우 비어 있습니다. |  |
| 400/잘못 된 요청 |  | 요청 구문 형식이 잘못 되었습니다. 요청 매개 변수가 잘못 되었습니다. |
|

## <a name="look-up-a-watchlist-by-name"></a>이름으로 관심 목록 조회

이 명령은 해당 항목을 제외 하 고 작업 영역과 연결 된 특정 관심 목록을 검색 합니다.

### <a name="request-uri"></a>요청 URI
(URI는 한 줄 이며 가독성을 높이기 위해 분리 됨)

| 방법 | 요청 URI |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>응답

| 상태 코드 | 응답 본문 | 설명 |
|-|-|-|
| 200/양호 | 요청 된 관심 목록 |  |
| 400/잘못 된 요청 |  | 요청 구문 형식이 잘못 되었습니다. 요청 매개 변수가 잘못 되었습니다. |
| 404/찾을 수 없음 |  | 요청 된 이름의 관심 목록을 찾을 수 없습니다. |
|

## <a name="create-a-watchlist"></a>관심 목록 만들기

이 명령은 관심 목록을 만들고 여기에 항목을 추가 합니다. 관심 목록 및 해당 항목을 만들기 위해이 끝점에 대 한 두 호출을 수행 합니다. 첫 번째는 관심 목록 (parent)를 만들고 두 번째는 항목을 추가 합니다.

### <a name="request-uri"></a>요청 URI
(URI는 한 줄 이며 가독성을 높이기 위해 분리 됨)

| 방법 | 요청 URI |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>요청 본문

다음은 관심 목록 create 요청의 요청 본문 샘플입니다.

```json
{
    "properties": {
        "displayName": "High Value Assets Watchlist",
        "source": "Local file",
        "provider": "Microsoft",
        "numberOfLinesToSkip":"1",
        "rawContent": "metadata line\nheader1, header2\nval1, val2",
        "contentType": "text/csv"
    }
}
```

### <a name="responses"></a>응답

| 상태 코드 | 응답 본문 | 설명 |
|-|-|-|
| 200/양호 | 항목 없이 요청에 의해 생성 된 관심 목록 |  |
| 400/잘못 된 요청 |  | 요청 구문 형식이 잘못 되었습니다. 요청 매개 변수가 잘못 되었습니다. |
| 409/충돌 |  | 작업에 실패 했습니다. 별칭이 동일한 기존 관심 목록가 있습니다. |
|

## <a name="delete-a-watchlist"></a>관심 목록 삭제

이 명령은 관심 목록 및 해당 항목을 삭제 합니다.

### <a name="request-uri"></a>요청 URI
(URI는 한 줄 이며 가독성을 높이기 위해 분리 됨)

| 방법 | 요청 URI |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>응답

| 상태 코드 | 응답 본문 | 설명 |
|-|-|-|
| 200/양호 | 빈 응답 본문 |  |
| 204/콘텐츠 없음 | 빈 응답 본문 | 삭제 된 항목이 없습니다. |
| 400/잘못 된 요청 |  | 요청 구문 형식이 잘못 되었습니다. 요청 매개 변수가 잘못 되었습니다. |
|

## <a name="add-or-update-a-watchlist-item"></a>관심 목록 항목 추가 또는 업데이트

이 명령이 기존 *watchlisItemId* (GUID)에서 실행 되는 경우 요청 본문에 제공 된 데이터를 사용 하 여 항목을 업데이트 합니다. 그렇지 않으면 새 항목이 생성 됩니다.

### <a name="request-uri"></a>요청 URI
(URI는 한 줄 이며 가독성을 높이기 위해 분리 됨)

| 방법 | 요청 URI |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>요청 본문

다음은 관심 목록 항목 추가/업데이트 요청에 대 한 요청 본문의 샘플입니다.

```json
{
    "properties": {
      "itemsKeyValue": {
           "Gateway subnet": "10.0.255.224/27",
           "Web Tier": "10.0.1.0/24",
           "Business tier": "10.0.2.0/24",
           "Private DMZ in": "10.0.0.0/27",
           "Public DMZ out": "10.0.0.96/27"
      }
}
}
```

### <a name="responses"></a>응답

| 상태 코드 | 응답 본문 | 설명 |
|-|-|-|
| 200/양호 | 요청에 의해 만들어지거나 업데이트 된 관심 목록 항목입니다. |  |
| 400/잘못 된 요청 |  | 요청 구문 형식이 잘못 되었습니다. 요청 매개 변수가 잘못 되었습니다. |
| 409/충돌 |  | 작업에 실패 했습니다. 별칭이 동일한 기존 관심 목록가 있습니다. |
|

## <a name="delete-a-watchlist-item"></a>관심 목록 항목 삭제

이 명령은 기존 관심 목록 항목을 삭제 합니다.

### <a name="request-uri"></a>요청 URI
(URI는 한 줄 이며 가독성을 높이기 위해 분리 됨)

| 방법 | 요청 URI |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="responses"></a>응답

| 상태 코드 | 응답 본문 | 설명 |
|-|-|-|
| 200/양호 | 빈 응답 본문 |  |
| 204/콘텐츠 없음 | 빈 응답 본문 | 삭제 된 항목이 없습니다. |
| 400/잘못 된 요청 |  | 요청 구문 형식이 잘못 되었습니다. 요청 매개 변수가 잘못 되었습니다. |
|

## <a name="next-steps"></a>다음 단계

이 문서에서는 Log Analytics API를 사용 하 여 Azure 센티널에서 watchlists 및 해당 항목을 관리 하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Watchlists](watchlists.md) 에 대 한 자세한 정보
- [Azure 센티널 API](/rest/api/securityinsights/) 의 다른 용도 살펴보기