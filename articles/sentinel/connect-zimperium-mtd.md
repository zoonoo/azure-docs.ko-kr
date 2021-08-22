---
title: Zimperium Mobile Threat Defense를 Azure Sentinel에 연결 | Microsoft Docs
description: Zimperium Mobile Threat Defense를 Azure Sentinel에 연결하는 방법을 알아봅니다.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 840d87fe1bf14e60cdfdd2438189763bcc548c3a
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538310"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Zimperium Mobile Threat Defense를 Azure Sentinel에 연결


> [!IMPORTANT]
> Azure Sentinel의 Zimperium Mobile Threat Defense 데이터 커넥터는 현재 공개 미리 보기로 제공됩니다.
> 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


Zimperium Mobile Threat Defense 커넥터를 통해 Zimperium 위협 로그를 Azure Sentinel과 연결하여 대시보드를 확인하고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이를 통해 조직의 모바일 위협 환경에 대한 자세한 인사이트를 제공하고 보안 작업 기능을 향상시킬 수 있습니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Zimperium Mobile Threat Defense 구성 및 연결

Zimperium Mobile Threat Defense는 로그를 **Azure Sentinel** 로 직접 통합하고 내보낼 수 있습니다.

1. Azure Sentinel 포털에서 데이터 커넥터를 클릭하고 **Zimperium Mobile Threat Defense** 를 선택합니다.
2. **커넥터 페이지 열기** 를 선택합니다.
3. 다음과 같이 요약된 **Zimperium Mobile Threat Defense** 커넥터 페이지의 지침을 따릅니다.
 1. zConsole의 탐색 모음에서 **관리** 를 클릭합니다.
 2. **통합** 탭을 클릭합니다.
 3. **위협 보고** 단추를 클릭한 다음, **통합 추가** 단추를 클릭합니다.
 4. 사용 가능한 통합에서 **Microsoft Azure Sentinel** 을 선택하여 통합을 만들고 Azure Sentinel에 연결하기 위한 작업 영역 ID 및 기본 키를 입력합니다.
 5. Azure Sentinel에 푸시할 위협 데이터의 필터 수준을 선택하는 옵션을 사용할 수도 있습니다. 

4. 추가 정보는 [Zimperium 고객 지원 포털](https://support.zimperium.com)을 참조하세요.


## <a name="find-your-data"></a>데이터 찾기

성공적으로 연결되면 Log Analytics의 CustomLogs ZimperiumThreatLog_CL 및 ZimperiumMitigationLog_CL 아래에 데이터가 나타납니다.

Log Analytics에서 Zimperium Mobile Threat Defense에 대한 관련 스키마를 사용하려면 ZimperiumThreatLog_CL 및 ZimperiumMitigationLog_CL을 검색합니다.


## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Zimperium Mobile Threat Defense를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.

- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.

- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.

Zimperium에 대해 자세히 알아보려면 다음을 참조하세요.

- [Zimperium](https://zimperium.com)

- [Zimperium 모바일 보안 블로그](https://blog.zimperium.com)

- [Zimperium 고객 지원 포털](https://support.zimperium.com)