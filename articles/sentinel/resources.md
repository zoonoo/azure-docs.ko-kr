---
title: Azure 센티넬로 작업할 때 유용한 리소스 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Sentinel로 작업할 때 유용한 리소스 목록을 제공합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 2110d3319cebf693ef06deec26a29fa655e35035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585274"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Azure 센티넬 작업을 위한 유용한 리소스



이 문서에는 Azure Sentinel 작업에 대한 자세한 정보를 얻는 데 도움이 되는 리소스가 나열되어 있습니다.

Azure 논리 앱 커넥터:<https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>감사 및 보고
Azure Sentinel의 감사 로그는 [Azure 활동 로그에서](../azure-monitor/platform/platform-logs-overview.md)유지됩니다.

지원되는 다음 작업을 감사할 수 있습니다.

|작업 이름|    리소스 유형|
|----|----|
|통합 문서 만들기 또는 업데이트  |마이크로소프트.인사이트/통합 문서|
|통합 문서 삭제    |마이크로소프트.인사이트/통합 문서|
|워크플로 설정   |Microsoft.Logic/workflows|
|워크플로 삭제    |Microsoft.Logic/workflows|
|저장된 검색 만들기    |마이크로소프트.오퍼리인사이트/작업 공간/저장된검색|
|저장된 검색 삭제    |마이크로소프트.오퍼리인사이트/작업 공간/저장된검색|
|경고 규칙 업데이트 |마이크로소프트.시큐리티인사이트/경고규칙|
|경고 규칙 삭제 |마이크로소프트.시큐리티인사이트/경고규칙|
|경고 규칙 응답 작업 업데이트 |마이크로소프트.보안인사이트/경고규칙/작업|
|경고 규칙 응답 작업 삭제 |마이크로소프트.보안인사이트/경고규칙/작업|
|책갈피 업데이트   |마이크로소프트.시큐리티인사이트/북마크|
|책갈피 삭제   |마이크로소프트.시큐리티인사이트/북마크|
|서비스 케이스 업데이트   |마이크로소프트.시큐리티인사이트/케이스|
|서비스 케이스 조사 업데이트  |마이크로소프트.보안인사이트/사례/조사|
|사례 주석 만들기   |마이크로소프트.시큐리티인사이트/케이스/코멘트|
|데이터 커넥터 업데이트 |마이크로소프트.시큐리티인사이트/데이터커넥터|
|데이터 커넥터 삭제 |마이크로소프트.시큐리티인사이트/데이터커넥터|
|업데이트 설정    |마이크로소프트.시큐리티인사이트/설정|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Azure Sentinel에서 감사 및 보고 데이터 보기

Azure Activity 로그에서 Azure Sentinel로 스트리밍하여 이 데이터를 볼 수 있으며 이 에 대한 조사 및 분석을 수행할 수 있습니다.

1. Azure [활동](connect-azure-activity.md) 데이터 원본을 연결합니다. 이렇게 하면 감사 이벤트가 AzureActivity라는 **로그** 화면의 새 테이블로 스트리밍됩니다.
2. 그런 다음 다른 테이블과 마찬가지로 KQL을 사용하여 데이터를 쿼리합니다.



## <a name="vendor-documentation"></a>공급업체 문서

| **공급 업체**  | **Azure 센티넬에서 인시던트 사용** | **링크**|
|----|----|----|
| GitHub| 커뮤니티 페이지에 액세스하는 데 사용됩니다.| <https://github.com/Azure/Azure-Sentinel> |
| 팔로알토| CEF 구성| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| 복수시력 | Kusto 쿼리 언어 과정| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>블로그 및 포럼

Azure Sentinel의 [TechCommunity 공간에](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) 질문을 게시합니다.

[테크 커뮤니티](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) 및 마이크로 [소프트 Azure에서](https://azure.microsoft.com/blog/tag/azure-sentinel/)Azure 센티넬 블로그 게시물보기 .


## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel으로 작업할 때 유용한 리소스 목록을 얻었습니다. Microsoft Azure 보안 및 규정 준수 [블로그에서](https://blogs.msdn.com/b/azuresecurity/)Azure 보안 및 규정 준수에 대한 추가 정보를 찾을 수 있습니다.
