---
title: '자습서: Azure Portal을 사용하여 지역 간 부하 분산 장치 만들기'
titleSuffix: Azure Load Balancer
description: 이 자습서를 시작하여 Azure Portal을 사용하여 지역 간 Azure Load Balancer를 배포합니다.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/24/2021
ms.openlocfilehash: 16320021ede4a4e285c4e1973c166d2cdf643c4a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529533"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 지역 간 Azure Load Balancer 만들기

지역 간 부하 분산 장치는 여러 Azure 지역에서 서비스를 전역적으로 사용할 수 있도록 합니다. 한 지역이 실패하는 경우 트래픽이 다음의 가장 가까운 정상적인 지역 부하 분산 장치로 라우팅됩니다.  

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 지역 간 부하 분산 장치 만들기
> * 두 개의 지역 부하 분산 장치를 포함하는 백 엔드 풀 만들기
> * 부하 분산 장치 규칙 만들기
> * 부하 분산 장치 테스트

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

> [!IMPORTANT]
> 지역 간 Azure Load Balancer는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독
- 두 개의 서로 다른 Azure 지역에 배포된 백 엔드 풀을 사용하는 두 개의 **표준** SKU Azure Load Balancer
    - 지역 표준 부하 분산 장치 및 백 엔드 풀에 대한 가상 머신을 만드는 방법에 대한 자세한 내용은 [빠른 시작: Azure Portal을 사용하여 VM 부하를 분산하는 공용 부하 분산 장치 만들기](quickstart-load-balancer-standard-public-portal.md)를 참조하세요.
        - 각 지역의 부하 분산 장치, 가상 머신 및 기타 리소스의 이름을 **-R1** 및 **-R2** 를 사용하여 추가합니다. 

## <a name="sign-in-to-azure-portal"></a>Azure Portal에 로그인

Azure Portal에 [로그인](https://portal.azure.com)합니다.

## <a name="create-cross-region-load-balancer"></a>지역 간 부하 분산 장치 만들기

이 섹션에서는 지역 간 부하 분산 장치 및 공용 IP 주소를 만듭니다.

1. **리소스 만들기** 를 선택합니다. 
2. 검색 상자에 **부하 분산 장치** 를 입력합니다. 검색 결과에서 **부하 분산 장치** 를 선택합니다.
3. **부하 분산 장치** 페이지에서 **만들기** 를 선택합니다.
4. **부하 분산 장치 만들기** 페이지의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택합니다. 

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | Subscription               | 구독을 선택합니다.    |    
    | Resource group         | **새로 만들기** 를 선택하고 텍스트 상자에 **CreateCRLBTutorial-rg** 를 입력합니다.|
    | Name                   | **myLoadBalancer-CR** 을 입력합니다.                                   |
    | 지역         | **(미국) 미국 서부** 를 선택합니다.                                        |
    | Type          | **공용** 을 선택합니다.                                        |
    | SKU           | 기본값인 **표준** 을 그대로 둡니다. |
    | 계층           | **글로벌** 을 선택합니다. |
    | 공용 IP 주소 | **새로 만들기** 를 선택합니다.|
    | 공용 IP 주소 이름 | 텍스트 상자에 **myPublicIP-CR** 을 입력합니다.|
    | 라우팅 기본 설정| **Microsoft 네트워크** 를 선택합니다. </br> 라우팅 기본 설정에 대한 자세한 내용은 [라우팅 기본 설정(미리 보기)이란?](../virtual-network/routing-preference-overview.md)을 참조하세요. |

    > [!NOTE]
    > 지역 간 부하 분산 장치는 다음 홈 지역에서만 배포할 수 있습니다. **미국 동부 2, 미국 서부, 서유럽, 동남아시아, 미국 중부, 북유럽, 동아시아** 자세한 내용은 **https://aka.ms/homeregionforglb** 을(를) 참조하세요.


3. 나머지 설정에는 기본값을 적용한 다음, **검토 + 만들기** 를 선택합니다.

4. **검토 + 만들기** 탭에서 **만들기** 를 선택합니다.   

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="지역 간 부하 분산 장치 만들기" border="true":::

## <a name="create-backend-pool"></a>백 엔드 풀 만들기

이 섹션에서는 지역 간 부하 분산 장치의 백 엔드 풀에 두 개의 지역 표준 부하 분산 장치를 추가합니다.

> [!IMPORTANT]
> 이러한 단계를 완료하려면 백 엔드 풀을 사용하는 두 개의 지역 부하 분산 장치가 구독에 배포되어 있는지 확인합니다.  자세한 내용은 **[빠른 시작: Azure Portal을 사용하여 VM 부하를 분산하는 공용 부하 분산 장치 만들기](quickstart-load-balancer-standard-public-portal.md)** 를 참조하세요.

지역 표준 부하 분산 장치를 포함하도록 백 엔드 주소 풀 **myBackendPool-CR** 을 만듭니다.

1. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고 **모든 리소스** 를 선택한 다음, 리소스 목록에서 **myLoadBalancer-CR** 을 선택합니다.

2. **설정** 에서 **백 엔드 풀** 을 선택한 다음, **추가** 를 선택합니다.

3. **백 엔드 풀 추가** 페이지에서 이름에 **myBackendPool-CR** 을 입력합니다.

4. **추가** 를 선택합니다.

4. **myBackendPool-CR** 을 선택합니다.

5. **부하 분산 장치** 에서 **부하 분산 장치** 아래의 풀 다운 상자를 선택합니다.

5. **myLoadBalancer-R1** 또는 지역 1에서 부하 분산 장치의 이름을 선택합니다.

6. **프런트 엔드 IP 구성** 에서 풀 다운 상자를 선택합니다. **LoadBalancerFrontEnd** 를 선택합니다.

7. 4-6단계를 반복하여 **myLoadBalancer-R2** 를 추가합니다.

8. **추가** 를 선택합니다.

    :::image type="content" source="./media/tutorial-cross-region-portal/add-to-backendpool.png" alt-text="백 엔드 풀에 지역 부하 분산 장치 추가" border="true":::

## <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

이 섹션에서 만드는 부하 분산 장치 규칙은 다음과 같습니다.

* 이름은 **myHTTPRule** 입니다.
* 프런트 엔드에서 이름은 **LoadBalancerFrontEnd** 입니다.
* **포트 80** 에서 수신 대기합니다.
* 부하 분산된 트래픽을 **포트 80** 에서 **myBackendPool-CR** 이라는 백 엔드로 보냅니다.

    > [!NOTE]
    > 프런트 엔드 포트는 백 엔드 포트 및 백 엔드 풀에 있는 지역 부하 분산 장치의 프런트 엔드 포트와 일치해야 합니다.

1. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고 **모든 리소스** 를 선택한 다음, 리소스 목록에서 **myLoadBalancer-CR** 을 선택합니다.

2. **설정** 아래에서 **부하 분산 규칙** 을 선택한 다음, **추가** 를 선택합니다.

3. 다음 값을 사용하여 부하 분산 규칙을 구성합니다.
    
    | 설정 | 값 |
    | ------- | ----- |
    | 속성 | **myHTTPRule** 을 입력합니다. |
    | IP 버전 | **IPv4** 를 선택합니다. |
    | 프런트 엔드 IP 주소 | **LoadBalancerFrontEnd** 를 입력합니다. |
    | 프로토콜 | **TCP** 를 선택합니다. |
    | 포트 | **80** 을 입력합니다.|
    | 백 엔드 포트 | **80** 을 입력합니다. |
    | 백 엔드 풀 | **myBackendPool** 을 선택합니다.|
    | 유휴 제한 시간(분) | 슬라이더를 **15** 로 이동합니다. |
    | TCP 재설정 | **사용** 을 선택합니다. |

4. 나머지는 기본값으로 둔 다음, **확인** 을 선택합니다.

    :::image type="content" source="./media/tutorial-cross-region-portal/create-lb-rule.png" alt-text="부하 분산 장치 규칙 만들기" border="true":::

## <a name="test-the-load-balancer"></a>부하 분산 장치 테스트

이 섹션에서는 지역 간 부하 분산 장치를 테스트합니다. 웹 브라우저에서 공용 IP 주소에 연결합니다.  지역 부하 분산 장치 백 엔드 풀 중 하나에서 가상 머신을 중지하고 장애 조치(failover)를 관찰합니다.

1. **개요** 화면에서 부하 분산 장치의 공용 IP 주소를 찾습니다. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고 **모든 리소스** 를 선택한 다음, **myPublicIP-CR** 을 선택합니다.

2. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. IIS 웹 서버의 기본 페이지가 브라우저에 표시됩니다.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="부하 분산 장치 테스트" border="true":::

3. 지역 부하 분산 장치 중 하나의 백 엔드 풀에서 가상 머신을 중지합니다.

4. 웹 브라우저를 새로 고치고 다른 지역 부하 분산 장치에 대한 연결의 장애 조치(failover)를 관찰합니다.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="장애 조치(failover) 후 부하 분산 장치 테스트" border="true":::

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 리소스 그룹, 부하 분산 장치 및 모든 관련 리소스를 삭제합니다. 

이렇게 하려면 리소스가 포함된 **CreateCRLBTutorial-rg** 리소스 그룹을 선택한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 작업을 수행했습니다.

* 지역 간 부하 분산 장치 만들기
* 지역 간 부하 분산 장치의 백 엔드 풀에 지역 부하 분산 장치 추가
* 부하 분산 규칙 만들기
* 부하 분산 장치 테스트

지역 간 부하 분산 장치에 대한 자세한 내용은 다음을 참조하세요.
> [!div class="nextstepaction"]
> [지역 간 부하 분산 장치(미리 보기)](cross-region-overview.md)
