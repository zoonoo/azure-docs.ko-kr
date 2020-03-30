---
title: 지페륨 모바일 위협 방어를 Azure 센티넬에 연결 | 마이크로 소프트 문서
description: Zimperium 모바일 위협 방어를 Azure 센티넬에 연결하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 86854fa22a49f09e5d3d2fc5fdb53c245850fbac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587943"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>지퍼륨 모바일 위협 방어를 Azure 센티넬에 연결


> [!IMPORTANT]
> Azure Sentinel의 Zimperium 모바일 위협 방어 데이터 커넥터는 현재 공개 미리 보기 상태입니다.
> 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.



Zimperium 모바일 위협 방어 커넥터를 사용하면 Zimperium 위협 로그를 Azure Sentinel과 연결하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이를 통해 조직의 모바일 위협 환경에 대한 더 많은 통찰력을 얻고 보안 운영 기능을 향상시킬 수 있습니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행 중인 작업 영역의 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>짐페륨 모바일 위협 방어 구성 및 연결

Zimperium 모바일 위협 방어 통합 하 고 **Azure 센티넬에**직접 로그를 내보낼 수 있습니다.

1. Azure Sentinel 포털에서 데이터 커넥터를 클릭하고 **Zimperium 모바일 위협 방어를**선택합니다.
2. **연결선 열기 페이지를**선택합니다.
3. **Zimperium 모바일 위협 방어** 커넥터 페이지의 지침을 따르십시오.
 1. zConsole에서 탐색 모음에서 **관리를** 클릭합니다.
 2. **통합** 탭을 클릭합니다.
 3. 위협 **보고** 단추를 클릭한 다음 **통합 추가 단추를 클릭합니다.**
 4. 사용 가능한 통합에서 **Microsoft Azure Sentinel을** 선택하여 통합을 만들고 Azure Sentinel에 연결하는 작업 영역 ID 및 기본 키를 입력합니다.
 5. Azure Sentinel에 푸시할 위협 데이터에 대한 필터 수준을 선택하는 옵션도 사용할 수 있습니다. 

4. 자세한 내용은 [Zimperium 고객 지원 포털을](https://support.zimperium.com)참조하십시오.


## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정되면 사용자 지정 로그인 ZimperiumThreatLog_CL 및 ZimperiumMitigationLog_CL 아래의 로그 분석에 데이터가 표시됩니다.

Zimperium 모바일 위협 방어를 위해 로그 분석에서 관련 스키마를 사용하려면 ZimperiumThreatLog_CL 검색하고 ZimperiumMitigationLog_CL.


## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 로그 분석에 표시되기 시작할 때까지 20분 이상이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Zimperium 모바일 위협 방어를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.

- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.

짐페리움에 대해 자세히 알아보려면 다음을 참조하십시오.

- [Zimperium](https://zimperium.com)

- [짐페리움 모바일 보안 블로그](https://blog.zimperium.com)

- [짐페리움 고객 지원 포털](https://support.zimperium.com)

