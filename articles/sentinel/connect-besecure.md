---
title: 보안 beSECURE 데이터를 넘어 Azure 센티널에 연결 | Microsoft Docs
description: 외부 beSECURE 데이터 커넥터를 사용 하 여 beSECURE 로그를 Azure 센티널로 가져오는 방법에 대해 알아봅니다. 통합 문서에서 beSECURE 데이터를 보고, 경고를 생성 하 고, 조사를 개선 합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 313f201aeabd470850b27d979dc5253f80e82a55
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541168"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>Azure 센티널에 보안 beSECURE 이상 연결

> [!IMPORTANT]
> 외부 보안 beSECURE 커넥터는 현재 **미리 보기로** 제공 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

이외 Security beSECURE 커넥터를 사용 하면 모든 beSECURE 보안 솔루션 로그를 Azure 센티널에 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. BeSECURE와 Azure 센티널 간의 통합은 REST API를 사용 합니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="configure-and-connect-besecure"></a>BeSECURE 구성 및 연결

beSECURE는와 통합 하 고 로그를 Azure 센티널로 직접 내보낼 수 있습니다.

1. Azure 센티널 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. **데이터 커넥터** 갤러리에서 **외부 보안 BeSECURE (미리 보기)** 를 선택한 다음 **커넥터 페이지를 엽니다**.

1. 아래 단계에 따라 검색 결과를 전송 하 고, 상태를 검색 하 고, 추적 로그를 Azure 센티널로 beSECURE 솔루션을 구성 합니다.

    **통합 메뉴에 액세스 합니다.**
    1. ' 자세히 ' 메뉴 옵션을 클릭 합니다.

    1. 서버 선택

    1. 통합 선택

    1. Azure Sentinel 사용

    **Azure 센티널 설정을 사용 하 여 beSECURE를 제공 합니다.**

      Azure 센티널 커넥터 페이지에서 *작업 영역 ID* 및 *기본 키* 값을 복사 하 고 beSECURE 구성에 붙여넣은 후 **수정** 을 클릭 합니다.
      
      :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{작업 영역 ID 및 기본 키}":::

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 데이터는 **로그** 에서 **customlogs** 섹션의 다음 테이블 중 하나 이상에 표시 됩니다.
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

분석 규칙, 구하기 쿼리, 조사 또는 Azure 센티널의 다른 위치에서 beSECURE 로그를 쿼리하려면 쿼리 창의 맨 위에 위의 테이블 이름 중 하나를 입력 합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사
로그가 Log Analytics 나타날 때까지 최대 20 분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 beSECURE를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 확보](quickstart-get-visibility.md)하는 방법을 알아보세요.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
