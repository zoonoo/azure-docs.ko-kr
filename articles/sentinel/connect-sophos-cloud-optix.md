---
title: Sophos Cloud Optix data를 Azure 센티널에 연결 | Microsoft Docs
description: Sophos Cloud Optix 커넥터를 사용 하 여 <PRODUCT NAME> Azure 센티널로 로그를 가져오는 방법에 대해 알아봅니다. <PRODUCT NAME>통합 문서에서 데이터를 보고, 경고를 생성 하 고, 조사를 개선 합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/26/2021
ms.author: yelevin
ms.openlocfilehash: c66205ffd9bd5a742d645cbf2f9251a44329a16e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700873"
---
# <a name="connect-your-sophos-cloud-optix-to-azure-sentinel"></a>Sophos Cloud Optix를 Azure 센티널에 연결

> [!IMPORTANT]
> Sophos Cloud Optix 커넥터는 현재 **미리 보기로** 제공 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

Sophos Cloud Optix 커넥터를 사용 하면 모든 Sophos Cloud Optix 보안 솔루션 로그를 Azure 센티널과 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다.  이 기능은 조직의 클라우드 보안 및 규정 준수 상태에 대 한 자세한 정보를 제공 하 고 클라우드 보안 작업 기능을 향상 시킵니다. Sophos Cloud Optix와 Azure 센티널 간의 통합을 통해 REST API을 사용 합니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="configure-and-connect-sophos-cloud-optix"></a>Sophos Cloud Optix 구성 및 연결

Sophos Cloud Optix는 로그를 Azure 센티널로 직접 통합 하 고 내보낼 수 있습니다.

1. Azure 센티널 포털에서 **데이터 커넥터** 를 클릭 하 고 **Sophos Cloud Optix (미리 보기)** 를 선택 합니다.

1. **커넥터 페이지 열기** 를 선택 합니다.

1. 커넥터 페이지에서 **작업 영역 ID** 및 **기본 키** 를 복사 하 여 저장 합니다.

1. Sophos의 지침에 따라 두 번째 단계에서 시작 하 여 [센티널 Microsoft Azure와 통합](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html) 합니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 데이터는 *SophosCloudOptix_CL* 테이블의 **사용자 지정 로그** 섹션 아래에 있는 **로그** 에 표시 됩니다.

Sophos Cloud Optix 데이터를 쿼리하려면 `SophosCloudOptix_CL` 쿼리 창에를 입력 합니다. 몇 가지 유용한 쿼리 샘플은 커넥터 페이지의 **다음 단계** 탭과 [Sophos 설명서](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html)를 참조 하세요.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics 나타날 때까지 최대 20 분이 걸릴 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Sophos Cloud Optix를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대 한 가시성을 확보](quickstart-get-visibility.md)하는 방법을 알아보세요.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
