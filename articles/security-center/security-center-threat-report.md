---
title: Azure Security Center 위협 인텔리전스 보고서 | Microsoft Docs
description: 이 페이지를 사용 하 여 보안 경고에 대 한 자세한 정보를 확인 하는 동안 Azure Security Center 위협 인텔리전스 보고서를 사용 하는 데 도움이 됩니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: a4fdbab4a69fac1376779f37d5fa69fef587bf52
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84888217"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Azure Security Center 위협 인텔리전스 보고서

이 페이지에서는 Azure Security Center의 위협 인텔리전스 보고서를 통해 보안 경고를 트리거한 위협에 대해 자세히 알아볼 수 있는 방법에 대해 설명 합니다.


## <a name="what-is-a-threat-intelligence-report"></a>위협 인텔리전스 보고서란?

Security Center 위협 방지는 Azure 리소스, 네트워크 및 연결 된 파트너 솔루션의 보안 정보를 모니터링 하는 방식으로 작동 합니다. 위협을 식별하도록 종종 여러 소스의 정보를 상호 연결하는 이 정보를 분석합니다. 자세한 내용은 [Azure Security Center 검색 하 고 위협에 대응 하는 방법](security-center-alerts-overview.md#detect-threats)을 참조 하세요.

Security Center에서 위협을 식별 하는 경우 수정 제안을 포함 하 여 이벤트에 대 한 자세한 정보를 포함 하는 [보안 경고](security-center-managing-and-responding-alerts.md)를 트리거합니다. 인시던트 대응 팀이 위협을 조사 하 고 수정 하는 데 도움을 주는 Security Center 검색 된 위협에 대 한 정보가 포함 된 위협 인텔리전스 보고서를 제공 합니다 보고서에는 다음과 같은 정보가 포함 됩니다.

* 공격자의 ID 또는 연결(이 정보가 제공되는 경우)
* 공격자의 목표
* 현재 및 과거 공격 캠페인(이 정보가 제공되는 경우)
* 공격자의 전술, 도구 및 프로시저
* URL, 파일 해시 등 관련 IoC(보안 침해 지표)
* 피해자 유형(Azure 리소스가 위험한 상태에 있는지 결정하는 데 도움이 되는 산업적 유행 및 지리적 유행)
* 마이그레이션 및 수정 정보

> [!NOTE]
> 세부 수준이 맬웨어 활동 및 유행에 기반하기 때문에 특정 보고서의 정보 량은 다양하게 변합니다.

Security Center에는 세 가지 유형의 위협 보고서가 있는데 공격에 따라 달라질 수 있습니다. 제공되는 보고서는 다음과 같습니다.

* **활동 그룹 보고서**: 공격자, 목표 및 전술에 대 한 심층 다이브을 제공 합니다.
* **캠페인 보고서**: 특정 공격 캠페인의 세부 정보에 중점을 둡니다.
* **위협 요약 보고서**: 이전 두 보고서의 모든 항목을 포함하고 있습니다.

이 유형의 정보는 공격 원본, 공격자의 동기 및 나중에이 문제를 완화 하기 위해 수행할 작업을 이해 하는 데 지속적으로 조사 하는 사고 대응 프로세스 중에 유용 합니다.



## <a name="how-to-access-the-threat-intelligence-report"></a>어떻게 위협 인텔리전스 보고서에 액세스하는가?

1. Security Center의 사이드바에서 **보안 경고** 페이지를 엽니다.
1. 경고를 선택 합니다. 
    경고에 대 한 자세한 내용이 포함 된 경고 정보 페이지가 열립니다. 아래에는 **랜 섬 웨어 표시기 검색** 된 경고 세부 정보 페이지가 있습니다.

    [![랜 섬 웨어 표시기 검색 된 경고 정보 페이지](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. 보고서에 대 한 링크를 선택 하면 기본 브라우저에서 PDF가 열립니다.

    [![잠재적으로 안전 하지 않은 작업 경고 세부 정보 페이지](media/security-center-threat-report/threat-intelligence-report.png)](media/security-center-threat-report/threat-intelligence-report.png#lightbox)

    필요에 따라 PDF 보고서를 다운로드할 수 있습니다. 

    >[!TIP]
    > 각 보안 경고에 제공되는 정보 량은 경고 유형에 따라 달라집니다.



## <a name="next-steps"></a>다음 단계

이 페이지에서는 보안 경고를 조사할 때 위협 인텔리전스 보고서를 여는 방법에 대해 설명 했습니다. 관련 정보는 다음 페이지를 참조 하세요.

* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) 보안 경고를 관리하고 응답하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 인시던트 처리](security-center-incident.md)