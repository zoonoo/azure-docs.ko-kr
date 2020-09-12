---
title: Azure 스프링 클라우드의 문제를 자체 진단 하 고 해결 하는 방법
description: Azure 스프링 클라우드의 문제를 자체 진단 하 고 해결 하는 방법에 대해 알아봅니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.custom: devx-track-java
ms.openlocfilehash: d8f3b4d57f00ef33bbf566c36babafd83be9a5df
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299629"
---
# <a name="how-to-self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>Azure 스프링 클라우드의 문제를 자체 진단 하 고 해결 하는 방법
Azure 스프링 클라우드 진단은 앱 문제를 해결 하는 데 도움이 되는 대화형 환경입니다. 구성이 필요하지 않습니다. 문제가 발견 되 면 Azure 스프링 클라우드 진단은 문제를 해결 하 고 문제를 해결 하는 데 도움이 되는 정보를 안내해 줍니다.

## <a name="prerequisites"></a>전제 조건
이 자습서를 완료하려면 다음이 필요합니다.

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* 배포된 Azure Spring Cloud 서비스 인스턴스 [Azure CLI를 통한 앱 배포를 위한 빠른 시작](spring-cloud-quickstart.md)을 수행하여 시작하세요.
* 해당 서비스 인스턴스에서 이미 하나 이상의 애플리케이션이 생성되었습니다.

## <a name="navigate-to-the-diagnostics-page"></a>진단 페이지로 이동 합니다.
1. Azure Portal에 로그인합니다.
2. Azure Spring Cloud **개요** 페이지로 이동합니다.
3. 페이지의 왼쪽에 있는 메뉴에서 **진단 및 해결 문제를** 엽니다.

 ![진단, 해결 대화 상자](media/spring-cloud-diagnose/diagnose-solve-dialog.png)

 ## <a name="search-logged-issues"></a>기록 되는 검색 문제
문제를 찾으려면 키워드를 입력 하 여 검색 하거나 솔루션 그룹을 클릭 하 여 해당 범주의 모든 항목을 탐색할 수 있습니다.

 ![검색 문제](media/spring-cloud-diagnose/search-detectors.png)

**구성 서버 상태 검사**, **구성 서버 상태**또는 **구성 서버 업데이트 기록** 의 선택은 다양 한 결과를 표시 합니다.

![문제 옵션](media/spring-cloud-diagnose/detectors-options.png)

대상 탐지기를 찾아 클릭 하 여 실행 합니다. 탐지기를 실행 한 후 진단 요약이 표시 됩니다. **전체 보고서 보기** 를 선택 하 여 진단 정보를 확인 하거나 **타일 표시 메뉴** 단추를 클릭 하 여 탐지기 목록으로 돌아갈 수 있습니다.

 ![진단 요약](media/spring-cloud-diagnose/summary-diagnostics.png)

진단 정보 페이지에서 오른쪽 위 모서리의 컨트롤러를 사용 하 여 진단 시간 범위를 변경할 수 있습니다. 더 많은 메트릭 또는 로그를 보려면 각 진단을 설정/해제 합니다. 메트릭 및 로그에 대해 15 분 지연 될 수 있습니다.

 ![진단 세부 정보](media/spring-cloud-diagnose/diagnostics-details.png)

일부 결과는 관련 된 설명서를 포함 합니다.

 ![관련 세부 정보](media/spring-cloud-diagnose/related-details.png)

## <a name="next-steps"></a>다음 단계
* [경고 및 작업 그룹을 사용하여 Spring Cloud 리소스 모니터링](spring-cloud-tutorial-alerts-action-groups.md)
* [Azure Spring Cloud Service의 보안 컨트롤](spring-cloud-concept-security-controls.md)