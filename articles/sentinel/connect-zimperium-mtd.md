---
title: Zimperium 모바일 위협 방어를 Azure 센티널에 연결 | Microsoft Docs
description: Zimperium 모바일 위협 방어를 Azure 센티널에 연결 하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77587943"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Zimperium 모바일 위협 방어를 Azure 센티널에 연결


> [!IMPORTANT]
> Azure 센티널의 Zimperium Mobile Threat 방어 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.



Zimperium 모바일 위협 방어 커넥터는 Zimperium 위협 로그를 Azure 센티널과 연결 하 여 대시보드를 확인 하 고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있는 기능을 제공 합니다. 이를 통해 조직의 모바일 위협 환경에 대 한 자세한 정보를 제공 하 고 보안 작업 기능을 향상 시킬 수 있습니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Zimperium 모바일 위협 방어 구성 및 연결

Zimperium 모바일 위협 방어는 로그를 **Azure 센티널**로 직접 통합 하 고 내보낼 수 있습니다.

1. Azure 센티널 포털에서 데이터 커넥터를 클릭 하 고 **Zimperium 모바일 위협 방어**를 선택 합니다.
2. **커넥터 페이지 열기**를 선택 합니다.
3. **Zimperium 모바일 위협 방어** 커넥터 페이지의 지침에 따라 다음과 같이 요약 합니다.
 1. ZConsole의 탐색 모음에서 **관리** 를 클릭 합니다.
 2. **통합** 탭을 클릭합니다.
 3. **위협 보고** 단추를 클릭 한 다음 **통합 추가** 단추를 클릭 합니다.
 4. 사용 가능한 통합에서 **Microsoft Azure 센티널** 을 선택 하 고 작업 영역 ID 및 기본 키를 입력 하 여 Azure 센티널에 연결 하 여 통합을 만듭니다.
 5. Azure 센티널로 푸시할 위협 데이터의 필터 수준을 선택 하는 옵션을 사용할 수도 있습니다. 

4. 자세한 내용은 [Zimperium 고객 지원 포털](https://support.zimperium.com)을 참조 하세요.


## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 데이터는 CustomLogs ZimperiumThreatLog_CL 및 ZimperiumMitigationLog_CL 아래 Log Analytics에 나타납니다.

Zimperium 모바일 위협 방어를 위해 Log Analytics에서 관련 스키마를 사용 하려면 ZimperiumThreatLog_CL 및 ZimperiumMitigationLog_CL를 검색 합니다.


## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics 표시 되기 시작할 때까지 20 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Zimperium Mobile Threat 방어를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.

- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.

Zimperium에 대 한 자세한 내용은 다음을 참조 하세요.

- [Zimperium](https://zimperium.com)

- [Zimperium 모바일 보안 블로그](https://blog.zimperium.com)

- [Zimperium 고객 지원 포털](https://support.zimperium.com)

