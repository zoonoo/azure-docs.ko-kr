---
title: 클라우드 앱 보안 데이터를 Azure Sentinel에 연결 | 마이크로 소프트 문서
description: 클라우드 앱 보안 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 266d97e834247088d40837cbec1436e00d0f4be2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422153"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>마이크로소프트 클라우드 앱 보안에서 데이터를 연결 



[MCAS(Microsoft 클라우드 앱 보안)](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) 커넥터를 사용하면 MCAS에서 Azure Sentinel으로 경고 및 [클라우드 검색 로그를](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) 스트리밍할 수 있습니다. 이를 통해 클라우드 앱에 대한 가시성을 확보하고, 정교한 분석을 통해 사이버 위협을 식별 및 방지하고, 데이터 이동 방식을 제어할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 사용자는 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.
- 사용자는 작업 영역의 테넌트에 대한 글로벌 관리자 또는 보안 관리자 권한이 있어야 합니다.
- 클라우드 검색 로그를 Azure Sentinel로 스트리밍하려면 [Microsoft 클라우드 앱 보안에서 Azure Sentinel을 SIEM으로 사용하도록 설정합니다.](https://aka.ms/AzureSentinelMCAS)

> [!IMPORTANT]
> 클라우드 검색 로그의 섭취는 현재 공개 미리 보기상태입니다.
> 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
 
## <a name="connect-to-cloud-app-security"></a>Cloud App Security에 연결

이미 클라우드 앱 보안이 있는 경우 네트워크에서 활성화되어 있는지 [확인합니다.](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
클라우드 앱 보안이 배포되고 데이터를 수집하면 경고 데이터를 Azure Sentinel으로 쉽게 스트리밍할 수 있습니다.


1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터를 선택합니다.** 커넥터 목록에서 Microsoft 클라우드 앱 **보안** 타일을 클릭한 다음 오른쪽 하단의 **커넥터 열기 페이지** 단추를 클릭합니다.

1. Azure Sentinel로 스트리밍할 로그를 선택합니다. **경고** 및 클라우드 **검색 로그(미리 보기)를** 선택할 수 있습니다. 

1. **변경 내용 적용**을 클릭합니다.

1. 클라우드 앱 보안 경고에 대한 로그 분석에서 관련 `SecurityAlert` 스키마를 사용하려면 쿼리 창을 입력합니다. 클라우드 검색 로그 스키마의 `McasShadowItReporting`경우 을 입력합니다.

> [!NOTE]
> 클라우드 검색을 사용하면 클라우드 앱에 대한 사용자의 연결에 기반이 되는 데이터를 집계하여 추세를 감지하고 식별할 수 있습니다.
>
> 클라우드 검색 데이터는 일 단위로 집계되므로 최대 24시간 분량의 최신 데이터가 Azure Sentinel에 반영되지 않습니다. 하위 수준 조사에 더 즉각적인 데이터가 필요한 경우 원시 데이터가 있는 원본 어플라이언스 또는 서비스에서 직접 수행해야 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft 클라우드 앱 보안을 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [기본 제공](tutorial-detect-threats.md) 또는 [사용자 지정](tutorial-detect-threats-custom.md) 규칙을 사용하여 Azure Sentinel을 사용하여 위협 탐지를 시작하세요.
