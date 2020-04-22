---
title: Azure 키 볼트 모니터링 및 경고 | 마이크로 소프트 문서
description: 대시보드를 만들어 키 자격 증명 모음의 상태를 모니터링하고 경고를 구성합니다.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: cc0d969ff6eb76732768dfed2826762920ae9e62
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726944"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Azure 키 자격 증명 모음에 대한 모니터링 및 경고

## <a name="overview"></a>개요

키 자격 증명 모음을 사용하여 생산 비밀을 저장하기 시작하면 키 자격 증명 모음의 상태를 모니터링하여 서비스가 의도한 대로 작동하는지 확인하는 것이 중요합니다. 서비스 규모를 조정하기 시작하면 키 자격 증명 모음으로 전송되는 요청 수가 증가합니다. 이로 인해 요청 대기 시간이 증가하고 극단적인 경우 요청이 제한되어 서비스 성능에 영향을 줄 수 있습니다. 또한 키 자격 증명 모음에서 비정상적인 수의 오류 코드를 보내는 경우 알림을 받아야 하므로 액세스 정책 또는 방화벽 구성 문제에 대해 신속하게 알 수 있습니다. 이 문서에서는 다음 항목을 다룹니다.

+ 모니터링할 기본 키 볼트 메트릭
+ 메트릭을 구성하고 대시보드를 만드는 방법 
+ 지정된 임계값에서 경고를 만드는 방법 

## <a name="basic-key-vault-metrics-to-monitor"></a>모니터링할 기본 키 볼트 메트릭

+ 볼트 가용성  
+ 볼트 채도 
+ 서비스 API 대기 시간 
+ 총 서비스 API 조회(활동 유형별로 필터링) 
+ 오류 코드(상태 코드별로 필터링) 

**Vault 가용성** - 이 메트릭은 키 자격 증명 모음에 가동 중단이 발생했는지 빠르게 표시할 수 있으므로 항상 100%여야 합니다. 

**볼트 채도** – 키 자격 증명 모음이 제공할 수 있는 초당 요청 수는 수행중인 작업 유형에 따라 다다. 일부 볼트 작업의 요청당 임계값은 낮습니다. 이 메트릭은 모든 작업 유형에서 키 자격 증명 모음의 총 사용량을 집계하여 현재 키 자격 증명 모음 사용량을 나타내는 백분율 값을 제공합니다. 키 자격 증명 모음 서비스 제한의 전체 목록은 다음 문서를 참조하십시오. [Azure 키 볼트 서비스 제한](service-limits.md)

**서비스 API 대기 시간** - 이 메트릭은 키 자격 증명 모음에 대한 호출의 평균 대기 시간을 보여줍니다. 키 자격 증명 모음이 서비스 제한 내에 있을 수 있지만 키 자격 증명 모음의 사용률이 높으면 응용 프로그램 다운스트림이 실패하는 대기 시간이 발생할 수 있습니다. 

**총 API 조회** - 이 메트릭은 키 자격 증명 모음에 대한 모든 호출을 표시합니다. 이렇게 하면 키 자격 증명 모음을 호출하는 응용 프로그램을 식별하는 데 도움이 됩니다. 

**오류 코드** - 키 자격 증명 모음에 비정상적인 양의 오류가 발생하는 경우 이 메트릭이 표시됩니다. 오류 코드 및 문제 해결 지침의 전체 목록은 다음 문서를 참조하십시오. [Azure 키 볼트 REST API 오류 코드](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>메트릭을 구성하고 대시보드를 만드는 방법

1. Azure Portal에 로그인합니다.
2. 키 볼트로 이동
3. **모니터링** 중인 **메트릭** 선택 

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-1.png)

4. 차트의 제목을 대시보드에 표시할 내용으로 업데이트합니다. 
5. 범위를 선택합니다. 이 예제에서는 단일 키 자격 증명 모음을 선택합니다. 
6. **메트릭 전체 볼트 가용성** 및 집계 **평균** 선택 
7. 시간 범위를 마지막 24시간으로 업데이트하고 시간 세분성을 1분으로 업데이트합니다. 

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-2.png)

8. 볼트 포화 및 서비스 API 대기 시간 메트릭에 대해 위의 단계를 반복합니다. **대시보드에 고정을** 선택하여 지표를 대시보드에 저장합니다. 

> [!IMPORTANT]
> '대시보드에 고정'을 선택하고 구성한 모든 메트릭을 저장합니다. 페이지를 떠나 저장하지 않고 다시 돌아오면 구성 변경 내용이 손실됩니다. 

9. 키 자격 증명 모음의 모든 작업 유형을 모니터링하려면 **총 서비스 API 조회** 메트릭을 사용하고 활동 **유형별로 분할 적용을** 선택합니다.

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-3.png)

10. 키 자격 증명 모음에서 오류 코드를 모니터링하려면 **총 서비스 API 결과** 메트릭을 사용하고 활동 **유형별로 분할 적용을** 선택합니다.

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-4.png)

이제 다음과 같은 대시보드가 있습니다. 각 타일의 오른쪽 상단에 있는 3개의 점을 클릭하면 필요에 따라 타일의 크기를 다시 정렬하고 크기를 조정할 수 있습니다. 

대시보드를 저장하고 게시하면 Azure 구독에 새 리소스가 생성됩니다. "공유 대시보드"를 검색하여 언제든지 볼 수 있습니다. 

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>키 볼트에서 경고를 구성하는 방법 

이 섹션에서는 키 자격 증명 모음이 비정상 상태인 경우 즉시 조치를 취하도록 팀에 경고할 수 있도록 키 자격 증명 모음에 경고를 구성하는 방법을 보여 줍니다. 전자 메일을 보내는 경고를 구성할 수 있습니다.( 가급적 팀 DL에) 이벤트 그리드 알림을 발생하거나 전화 번호로 전화를 걸거나 문자를 보낼 수 있습니다. 고정 된 값에 따라 정적 경고를 선택하거나 모니터링된 메트릭이 정의된 시간 범위 내에서 키 볼트의 평균 제한을 초과하는 경우 경고하는 동적 경고를 선택할 수도 있습니다. 

> [!IMPORTANT]
> 새로 구성된 알림이 알림 전송을 시작하는 데 최대 10분이 걸릴 수 있습니다. 

### <a name="configure-an-action-group"></a>작업 그룹 구성 

작업 그룹은 알림 및 속성의 구성 가능한 목록입니다.

1. Azure Portal에 로그인합니다.
2. 검색 상자에서 **경고** 검색
3. **작업 관리** 선택

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-6.png)

4. **+ 작업 그룹 추가** 선택

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-7.png)

5. 작업 그룹의 **작업 유형을** 선택합니다. 이 예제에서는 전자 메일 경고를 만듭니다.

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-9.png)

6. 페이지 아래쪽에서 **확인**을 클릭합니다. 작업 그룹을 성공적으로 만들었습니다. 

작업 그룹을 구성했으니 키 볼트 경고 임계값을 구성합니다. 

### <a name="configure-alert-thresholds"></a>경고 임계값 구성 

1. Azure 포털에서 키 자격 증명 모음 리소스를 선택하고 **모니터링** 중 **경고를 선택합니다.**

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-10.png)

2. **새 경고 규칙** 선택

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-11.png)

3. 경고 규칙의 범위를 선택합니다. 단일 볼트 또는 여러 볼트를 선택할 수 있습니다. 

> [!IMPORTANT]
> 경고 범위에 대해 여러 볼트를 선택할 때 선택한 모든 볼트가 동일한 지역에 있어야 합니다. 다른 지역의 볼트에 대해 별도의 경고 규칙을 구성해야 합니다. 

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-12.png)

4. 경고에 대한 조건을 선택합니다. 다음 신호 중 하나를 선택하고 경고를 위한 논리를 정의할 수 있습니다. Key Vault 팀은 다음 경고 임계값을 구성하는 것이 좋습니다. 

    + 키 볼트 가용성이 100% 미만으로 떨어집니다(정적 임계값)
    + 키 볼트 대기 시간이 500ms(정적 임계값)보다 큽니다. 
    + 전체 볼트 채도가 75%(정적 임계값)보다 큽니다. 
    + 전체 볼트 채도가 평균을 초과합니다(동적 임계값)
    + 총 오류 코드가 평균보다 높음(동적 임계값) 

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>예 1: 대기 시간에 대한 정적 경고 임계값 구성

**전체 서비스 API 대기 시간을** 신호 이름으로 선택합니다.
> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-14.png)

다음 구성 매개 변수를 참조하십시오.

+ 임계값을 **정적으로** 설정 
+ 연산자를 **보다 큰** 것으로 설정
+ 집계 유형을 **평균으로** 설정
+ 임계값을 **500으로** 설정
+ 집계 기간을 **5분으로** 설정
+ 평가 빈도를 **1분으로** 설정
+ **완료**를 선택합니다.  

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>예 2: 볼트 채도에 대한 동적 경고 임계값 구성 

동적 경고를 사용하면 선택한 키 자격 증명 모음의 기록 데이터를 볼 수 있습니다. 파란색 영역은 키 자격 증명 모음의 평균 사용량을 나타냅니다. 빨간색 영역에는 경고 구성의 다른 조건이 충족되면 경고를 트리거한 스파이크가 표시됩니다. 빨간색 점은 집계된 시간 기간 동안 경고 기준이 충족된 위반 사례를 보여 준다. 정해진 시간 내에 특정 횟수의 위반 이후에 경고가 발생하도록 설정할 수 있습니다. 이전 데이터를 포함하지 않으려면 고급 설정에서 아래이전 데이터를 제외하는 옵션이 있습니다. 

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-16.png)

다음 구성 매개 변수를 참조하십시오.

+ 임계값을 **동적으로** 설정 
+ 연산자를 **보다 큰** 것으로 설정
+ 집계 유형을 **평균으로** 설정
+ 임계값 민감도를 **보통으로** 설정
+ 집계 기간을 **5분으로** 설정
+ 평가 빈도를 **1분으로** 설정
+ **선택 사항** 고급 설정 구성 
+ **완료**를 선택합니다.

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-17.png)

5. 구성한 작업 그룹 추가

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-18.png)

6. 경고 활성화 및 심각도 할당

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-19.png)

7. 경고 만들기 


## <a name="next-steps"></a>다음 단계

축하합니다, 당신은 지금 성공적으로 모니터링 대시 보드를 생성하고 키 볼트에 대한 경고를 구성! 위의 모든 단계를 수행한 후에는 키 자격 증명 모음이 구성한 경고 기준을 충족하면 전자 메일 알림을 받게 됩니다. 아래에 예제가 나와 있습니다. 이 문서에서 설정한 도구를 사용하여 키 자격 증명 모음의 상태를 적극적으로 모니터링합니다. 

### <a name="example-email-alert"></a>이메일 알림 예제 

> [!div class="mx-imgBorder"]
> ![Azure 포털의 스크린샷](../media/alert-20.png)
