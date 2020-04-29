---
title: Azure 애플리케이션 Insights를 사용 하 여 성능 및 부하 테스트 | Microsoft Docs
description: Azure 애플리케이션 Insights를 사용 하 여 성능 및 부하 테스트 설정
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77669865"
---
# <a name="performance-testing"></a>성능 테스트

> [!NOTE]
> 클라우드 기반 부하 테스트 서비스는 더 이상 사용 되지 않습니다. 사용 중단, 서비스 가용성 및 대체 서비스에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops)에서 찾을 수 있습니다.

Application Insights를 사용 하 여 웹 사이트에 대 한 부하 테스트를 생성할 수 있습니다. [가용성 테스트](monitor-web-app-availability.md)와 마찬가지로 전 세계의 Azure test agent에서 기본 요청 또는 [다단계 요청](availability-multistep.md) 을 보낼 수 있습니다. 성능 테스트를 통해 60 최대 2만 분 동안 최대 명의 동시 사용자를 시뮬레이션할 수 있습니다.

## <a name="create-an-application-insights-resource"></a>Application Insights 리소스 만들기

성능 테스트를 만들려면 먼저 Application Insights 리소스를 만들어야 합니다. 리소스를 이미 만든 경우 다음 섹션으로 진행 합니다.

Azure Portal에서 **리소스** > 만들기**개발자 도구** > **Application Insights** 를 선택 하 고 Application Insights 리소스를 만듭니다.

## <a name="configure-performance-testing"></a>성능 테스트 구성

처음으로 성능 테스트를 만드는 경우 **조직 설정** 을 선택 하 고 성능 테스트에 대 한 원본으로 사용할 Azure devops 조직을 선택 합니다.

**구성**아래에서 **성능 테스트** 로 이동 하 고 **새로** 만들기를 클릭 하 여 테스트를 만듭니다.

![웹 사이트의 최소 URL 채우기](./media/performance-testing/new-performance-test.png)

기본 성능 테스트를 만들려면 테스트 형식의 **수동 테스트** 를 선택 하 고 테스트에 필요한 설정을 입력 합니다.

|설정| 최댓값
|----------|------------|
| 사용자 부하 | 20,000 |
| 기간 (분)  | 60 |  

테스트를 만든 후 **테스트 실행**을 클릭 합니다.

테스트가 완료 되 면 아래와 유사한 결과가 표시 됩니다.

![테스트 결과](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Visual Studio 웹 테스트 구성

Application Insights 고급 성능 테스트 기능은 Visual Studio 성능 및 부하 테스트 프로젝트를 기반으로 빌드됩니다.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>다음 단계

* [다중 단계 웹 테스트](availability-multistep.md)
* [Url ping 테스트](monitor-web-app-availability.md)
