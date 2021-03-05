---
title: Azure 스프링 클라우드 VNET 자체를 진단 하는 방법
description: VNET에서 실행 되는 Azure 스프링 클라우드의 문제를 자체 진단 하 고 해결 하는 방법에 대해 알아봅니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5407213b62902326d53b73e42ee3af1ba9b11524
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125100"
---
# <a name="self-diagnose-running-azure-spring-cloud-in-vnet"></a>VNET에서 Azure 스프링 클라우드를 실행 하는 자체 진단
Azure 스프링 클라우드 진단은 구성 없이 가상 네트워크에서 실행 되는 대화형 문제 해결 앱을 지원 합니다. Azure 스프링 클라우드 진단은 문제를 식별 하 고 문제를 해결 하 고 해결 하는 데 도움이 되는 정보를 안내 합니다.

## <a name="navigate-to-the-diagnostics-page"></a>진단 페이지로 이동 합니다.
다음 절차에서는 네트워크 응용 프로그램에 대 한 진단을 시작 합니다.
1. Azure Portal에 로그인합니다.
1. Azure Spring Cloud 개요 페이지로 이동합니다.
1. 왼쪽 탐색 창의 메뉴에서 **문제 진단 및 해결을** 선택 합니다.
1. 세 번째 범주인 **네트워킹** 을 선택 합니다.

   ![자체 진단 제목](media/spring-cloud-self-diagnose-vnet/self-diagostic-title.png)

## <a name="view-a-diagnostic-report"></a>진단 보고서 보기
**네트워킹** 범주를 클릭 한 후에는 VNet 삽입 Azure 스프링 클라우드와 관련 된 네트워킹과 관련 된 두 가지 문제 ( **DNS 확인** 및 **필요한 아웃 바운드 트래픽**)를 볼 수 있습니다.

   ![자체 진단 옵션](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-req-outbound-options.png)

진단 보고서를 보려면 대상 문제를 선택 합니다. 진단에 대 한 요약이 다음과 같이 표시 됩니다. 

* *리소스가 제거 되었습니다.*
* *리소스가 사용자의 가상 네트워크에 배포 되지 않습니다*.

일부 결과는 관련 된 설명서를 포함 합니다. 다른 서브넷은 결과를 별도로 표시 합니다.

## <a name="dns-resolution"></a>DNS 확인 
**Dns 확인** 을 선택 하면 응용 프로그램에 dns 문제가 있는지 여부가 표시 됩니다.  정상적인 앱은 다음과 같이 나열 됩니다.

* *서브넷 ' subnet01 '의 문제 없이 DNS 문제가 해결* 되었습니다.
* *서브넷 ' subnet02 '의 문제 없이 DNS 문제가 해결* 되었습니다.

다음 진단 보고서 예제는 응용 프로그램의 상태를 알 수 없음을 나타냅니다. 보고 시간 프레임에는 상태가 보고 된 시간이 포함 되지 않습니다.  컨텍스트 종료 시간이 *2021-03-03T04:20:00Z* 인 것으로 가정 합니다. **DNS 확인 테이블 렌더링** 의 최신 타임 스탬프는 *2021-03-03t03:39:00z*, 이전 날짜입니다. 차단 된 네트워크로 인해 상태 검사 로그가 전송 되지 않았을 수 있습니다. 

알 수 없는 상태 결과는 관련 설명서를 포함 합니다.  왼쪽 꺾쇠 괄호를 클릭 하 여 드롭다운 표시를 볼 수 있습니다.

   ![DNS 알 수 없음](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-unknown.png)

사설 DNS 영역 레코드 집합을 잘못 구성 하면 다음과 같은 중요 한 결과를 얻게 됩니다 `Failed to resolve the Private DNS in subnet xxx` . 

드롭다운 **DNS 확인 테이블 렌더링** 에서 구성을 확인할 수 있는 세부 정보 메시지 정보를 찾을 수 있습니다.

## <a name="required-outbound-traffic"></a>필요한 아웃 바운드 트래픽 

**필요한 아웃 바운드 트래픽을** 선택 하는 경우 결과는 응용 프로그램에 아웃 바운드 트래픽 문제가 있는지 여부를 나타냅니다.  정상적인 앱은 다음과 같이 나열 됩니다.

* * 필수 아웃 바운드 트래픽이 ' subnet01 ' 서브넷에서 문제 없이 해결 되었습니다.
* * 필수 아웃 바운드 트래픽이 ' subnet02 ' 서브넷에서 문제 없이 해결 되었습니다.

서브넷이 NSG 또는 방화벽 규칙에 의해 차단 되 고 로그를 차단 하지 않은 경우 다음과 같은 오류가 표시 됩니다. [고객의 책임](spring-cloud-vnet-customer-responsibilities.md)을 간과 했는지 여부를 확인할 수 있습니다.
    
   ![끝점 실패](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-failed.png)

30 분 내에 데이터가 없는 경우 `Required Outbound Traffic Table Renderings` 결과는가 됩니다 `health status unknown` . 네트워크가 차단 되었거나 로그 서비스가 종료 된 것 같습니다.

   ![진단 끝점 알 수 없음](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-unknown.png)

## <a name="see-also"></a>참고 항목
* [Azure 스프링 클라우드를 자체 진단 하는 방법](spring-cloud-howto-self-diagnose-solve.md)