---
title: 성능 및 부하 테스트를 Azure Application Insights를 사용 하 여 | Microsoft Docs
description: 성능 및 Azure Application Insights를 사용 하 여 부하 테스트 설정
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 55d743e32f6db0828317d3764a97bcb35b104dad
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305098"
---
# <a name="performance-testing"></a>성능 테스트

> [!NOTE]
> 클라우드 기반 부하 테스트 서비스는 더 이상 사용 되지 않습니다. 사용 중단, 서비스 가용성 및 다른 서비스에 대 한 자세한 정보를 찾을 수 있습니다 [여기](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops)합니다.

Application Insights를 사용 하 여 웹 사이트에 대 한 부하 테스트를 생성할 수 있습니다. 와 같은 [가용성 테스트](monitor-web-app-availability.md), 두 기본 요청을 보낼 수 있습니다 또는 [다단계 요청](availability-multistep.md) Azure에서 테스트 전 세계는 에이전트입니다. 성능 테스트를 사용 하면 최대 60 분 동안 최대 2만 명의 동시 사용자를 시뮬레이션할 수 있습니다.

## <a name="create-an-application-insights-resource"></a>Application Insights 리소스 만들기

성능 테스트를 만들려면 먼저 Application Insights 리소스를 만들기 위한 해야 합니다. 이미 만든 경우 리소스는 다음 섹션으로 진행 합니다.

Azure portal에서 선택 **리소스 만들기** > **개발자 도구** > **Application Insights** Application Insights를 만들고 리소스입니다.

## <a name="configure-performance-testing"></a>성능 테스트 구성

처음으로 만드는 성능 테스트 선택 인지 **조직 설정** 성능 테스트에 대 한 원본으로 사용할 Azure DevOps 조직을 선택 합니다.

아래 **구성**로 이동 하세요 **성능 테스트** 클릭 **새** 테스트를 만드는 합니다.

![웹 사이트의 최소 URL 채우기](./media/performance-testing/new-performance-test.png)

기본 성능 테스트를 만들려면 테스트 유형을 선택 **수동 테스트** 테스트에 대해 원하는 설정을 입력 합니다.

|설정| 최대값
|----------|------------|
| 사용자 부하 | 20,000 |
| 기간 (분)  | 60 |  

테스트를 만든 후 클릭 **테스트 실행**합니다.

테스트가 완료 되 면 아래 결과 유사한 결과가 표시 됩니다.

![테스트 결과](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Visual Studio 웹 테스트 구성

Application Insights 고급 성능 테스트 기능을 Visual Studio 성능 및 부하를 기반으로 하며 프로젝트를 테스트 합니다.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>다음 단계

* [다중 단계 웹 테스트](availability-multistep.md)
* [Url ping 테스트](monitor-web-app-availability.md)