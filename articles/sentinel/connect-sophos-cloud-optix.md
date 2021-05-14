---
title: Sophos Cloud Optix 데이터를 Azure Sentinel에 연결하기 | Microsoft Docs
description: Sophos Cloud Optix 커넥터를 사용하여 <PRODUCT NAME> 로그를 Azure Sentinel에 가져오는 방법을 알아봅니다. 통합 문서에서 <PRODUCT NAME> 데이터를 보고, 경고를 만들고, 조사를 개선합니다.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700873"
---
# <a name="connect-your-sophos-cloud-optix-to-azure-sentinel"></a>Sophos Cloud Optix를 Azure Sentinel에 연결하기

> [!IMPORTANT]
> Sophos Cloud Optix 커넥터는 현재 **미리 보기** 중입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Sophos Cloud Optix 커넥터를 사용하면 Azure Sentinel에 모든 Sophos Cloud Optix 보안 솔루션 로그를 쉽게 연결하여, 대시보드를 보고, 사용자 지정 경고를 만들어, 조사 기능을 개선할 수 있습니다.  이러한 기능을 통하여 조직의 클라우드 보안과 규정 준수 상태에 대하여 더욱 깊은 인사이트를 가지고 클라우드 보안 작업 기능을 향상할 수 있습니다. Sophos Cloud Optix와 Azure Sentinel 간의 통합에는 REST API를 사용합니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-sophos-cloud-optix"></a>Sophos Cloud Optix 구성 및 연결하기

Sophos Cloud Optix는 로그를 Azure Sentinel로 직접 통합하고 내보낼 수 있습니다.

1. Azure Sentinel 포털에서, **데이터 커넥터** 를 클릭하고 **Sophos Cloud Optix(미리 보기)** 를 선택합니다.

1. **커넥터 페이지 열기** 를 선택합니다.

1. 커넥터 페이지에서 **작업 영역 ID** 와 **기본 키** 를 복사 후 저장합니다.

1. Sophos의 지침에 따라 세 번째 단계에서 시작하여 [Microsoft Azure Sentinel과 통합](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html)합니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정되면 데이터는 *SophosCloudOptix_CL* 테이블의 **사용자 지정 로그** 섹션에서 **로그** 에 표시됩니다.

Sophos Cloud Optix 데이터를 쿼리하려면, 쿼리 창에 `SophosCloudOptix_CL`과 같이 입력합니다. 유용한 쿼리 샘플 몇 가지를 참조하려면 커넥터 페이지의 **다음 단계** 탭과 [Sophos 설명서](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html)를 확인하세요.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시되는 데는 최대 20분이 걸립니다. 

## <a name="next-steps"></a>다음 단계

본 문서에서는 Sophos Cloud Optix를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 잠재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
