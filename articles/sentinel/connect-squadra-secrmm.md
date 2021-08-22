---
title: Azure Sentinel에 Squadra Technologies secRMM 데이터 연결 | Microsoft Docs
description: Azure Sentinel에 Squadra Technologies secRMM 데이터를 연결하는 방법을 알아봅니다.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: bd599fbfc712d85ec5fac0a3aa6f9f1cd320d91c
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538459"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Azure Sentinel에 Squadra Technologies secRMM 데이터 연결 

Squadra Technologies secRMM 커넥터를 사용하면 Squadra Technologies secRMM 보안 솔루션 로그를 Azure Sentinel에 쉽게 연결할 수 있습니다. 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이 커넥터는 USB 이동식 스토리지 이벤트에 대한 인사이트를 제공합니다. Squadra Technologies secRMM과 Azure Sentinel 간의 통합에서는 REST API를 사용합니다.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Squadra Technologies secRMM 구성 및 연결 

Squadra Technologies secRMM은 로그를 통합하고 직접 Azure Sentinel로 내보낼 수 있습니다.
1. Azure Sentinel 포털에서 데이터 커넥터를 클릭하고 Squadra Technologies secRMM 및 커넥터 페이지 열기를 차례로 선택합니다.

2. [Azure Sentinel에 대한 Squadra Technologies 온보딩 가이드](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf)에 설명된 단계에 따라 Azure Sentinel에서 Squadra secRMM 데이터를 가져옵니다.   

## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정되면 **로그** 의 **CustomLogs** 섹션에 있는 `secRMM_CL` 테이블에 로그가 표시됩니다.

Squadra Technologies secRMM 로그를 쿼리하려면 쿼리 창 맨 위에 테이블 이름을 입력합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Squadra Technologies secRMM을 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.