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
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588368"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>마이크로소프트 클라우드 앱 보안에서 데이터를 연결 



한 번의 클릭으로 [클라우드 앱 보안에서](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) Azure Sentinel로 로그를 스트리밍할 수 있습니다. 이 연결을 사용하면 클라우드 앱 보안에서 Azure Sentinel으로 경고를 스트리밍할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

- 글로벌 관리자 또는 보안 관리자 권한이 있는 사용자
- 클라우드 검색 로그를 Azure Sentinel로 스트리밍하려면 [Microsoft 클라우드 앱 보안에서 Azure Sentinel을 SIEM으로 사용하도록 설정합니다.](https://aka.ms/AzureSentinelMCAS)

> [!IMPORTANT]
> 클라우드 검색 로그의 섭취는 현재 공개 미리 보기상태입니다.
> 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
 
## <a name="connect-to-cloud-app-security"></a>Cloud App Security에 연결

이미 클라우드 앱 보안이 있는 경우 네트워크에서 활성화되어 있는지 [확인합니다.](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
클라우드 앱 보안이 배포되고 데이터를 수집하면 경고 데이터를 Azure Sentinel으로 쉽게 스트리밍할 수 있습니다.


1. Azure Sentinel에서 **데이터 커넥터를**선택하고 **클라우드 앱 보안** 타일을 클릭하고 **연결선 열기 페이지를**선택합니다.

1. Azure Sentinel로 스트리밍할 로그를 선택하고 **경고** 및 **클라우드 검색 로그(미리 보기)를** 선택할 수 있습니다. 

1. **연결**을 클릭합니다.

1. 클라우드 앱 보안 경고에 대한 로그 분석에서 관련 스키마를 사용하려면 **SecurityAlert**를 검색합니다.




## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft 클라우드 앱 보안을 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
