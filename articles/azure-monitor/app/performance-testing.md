---
title: Azure 응용 프로그램 인사이트를 통해 성능 및 부하 테스트 | 마이크로 소프트 문서
description: Azure 응용 프로그램 인사이트를 통해 성능 및 로드 테스트 설정
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669865"
---
# <a name="performance-testing"></a>성능 테스트

> [!NOTE]
> 클라우드 기반 부하 테스트 서비스가 더 이상 사용되지 않았습니다. 사용 중단, 서비스 가용성 및 대체 서비스에 대한 자세한 내용은 [여기에서](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops)확인할 수 있습니다.

응용 프로그램 인사이트를 사용하면 웹 사이트에 대한 부하 테스트를 생성할 수 있습니다. [가용성 테스트와](monitor-web-app-availability.md)마찬가지로 전 세계 Azure 테스트 에이전트의 기본 요청 또는 [다단계 요청을](availability-multistep.md) 보낼 수 있습니다. 성능 테스트를 통해 최대 60분 동안 최대 20,000명의 동시 사용자를 시뮬레이션할 수 있습니다.

## <a name="create-an-application-insights-resource"></a>Application Insights 리소스 만들기

성능 테스트를 만들려면 먼저 응용 프로그램 인사이트 리소스를 만들어야 합니다. 이미 리소스를 만든 경우 다음 섹션으로 진행합니다.

Azure 포털에서 리소스 > **개발자 도구** > **응용 프로그램 인사이트** **만들기를**선택하고 응용 프로그램 인사이트 리소스를 만듭니다.

## <a name="configure-performance-testing"></a>성능 테스트 구성

성능 테스트를 처음 만드는 경우 **조직 집합을** 선택하고 Azure DevOps 조직을 성능 테스트의 원본으로 선택합니다.

**구성에서**성능 **테스트로** 이동하여 **새로를** 클릭하여 테스트를 만듭니다.

![웹 사이트의 최소 URL 채우기](./media/performance-testing/new-performance-test.png)

기본 성능 테스트를 만들려면 **수동 테스트의** 테스트 유형을 선택하고 테스트에 필요한 설정을 입력합니다.

|설정| 최댓값
|----------|------------|
| 사용자 로드 | 20,000 |
| 지속 시간(분)  | 60 |  

테스트를 만든 후 **테스트 실행을**클릭합니다.

테스트가 완료되면 아래 결과와 유사한 결과가 표시됩니다.

![테스트 결과](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>비주얼 스튜디오 웹 테스트 구성

Application Insights 고급 성능 테스트 기능은 Visual Studio 성능 및 부하 테스트 프로젝트를 기반으로 구축됩니다.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>다음 단계

* [다중 단계 웹 테스트](availability-multistep.md)
* [URL 핑 테스트](monitor-web-app-availability.md)
