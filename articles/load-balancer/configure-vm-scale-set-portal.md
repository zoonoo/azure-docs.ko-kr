---
title: 기존 Azure 로드 밸런서 - Azure 포털을 사용 하 고 가상 시스템 규모 집합 구성
description: 기존 Azure 로드 밸런서를 사용하여 가상 시스템 확장 집합을 구성하는 방법에 대해 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: b2a83b226a4b2ddbbd554783de6a2b5c85c92f0a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349719"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Azure 포털을 사용하여 기존 Azure 로드 밸런서를 사용하여 가상 시스템 확장 집합 구성

이 문서에서는 기존 Azure 로드 밸런서를 사용하여 가상 시스템 확장 집합을 구성하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독
- 가상 시스템 크기 집합이 배포되는 구독의 기존 표준 sku 로드 밸런서입니다.
- 가상 시스템 규모 집합에 대한 Azure 가상 네트워크입니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

에서 [https://portal.azure.com](https://portal.azure.com)Azure 포털에 로그인합니다.



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>기존 로드 밸워서로 가상 시스템 스케일 세트 배포

이 섹션에서는 기존 Azure 로드 밸런서가 있는 Azure Portal에서 가상 시스템 규모 집합을 만듭니다.

> [!NOTE]
> 다음 단계에서는 **myVNet이라는** 가상 네트워크와 **myLoadBalancer라는** Azure 로드 밸러블러가 이전에 배포된 것으로 가정합니다.

1. 화면 왼쪽 상단에서 리소스 > **계산** > **가상 컴퓨터 규모 집합** **만들기를**클릭하거나 마켓플레이스 검색에서 **설정된 가상 컴퓨터 규모 집합을** 검색합니다.

2. **만들기**를 선택합니다.

3. **가상 시스템 배율 집합 만들기에서**기본 탭에서 이 정보를 입력하거나 **선택합니다.**

    | 설정                        | 값                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **프로젝트 세부 정보**            |                                                                                                       |
    | Subscription                   | Azure 구독 선택                                                                        |
    | 리소스 그룹                 | 새 만들기를 선택하고 **myResourceGroup을**입력한 다음 확인을 선택하거나 기존 리소스 그룹을 선택합니다. |
    | **세트 세부 사항 배율 조정**          |                                                                                                       |
    | 가상 머신 확장 집합 이름 | **myVMSS** 입력                                                                                      |
    | 지역                         | **미국 동부 2** 선택                                                                                    |
    | 가용성 영역              | 없음 을 **선택합니다.**                                                                                       |
    | **인스턴스 세부 정보**           |                                                                                                       |
    | 이미지                          | **우분투 서버 선택 18.04 LTS**                                                                    |
    | Azure 스팟 인스턴스            | **아니요**를 선택합니다.                                                                                         |
    | 크기                           | 기본값으로 둡니다.                                                                                      |
    | **관리자 계정**      |                                                                                                       |
    | 인증 유형            | **암호** 선택                                                                                   |
    | 사용자 이름                       | 관리자 사용자 이름 입력        |
    | 암호                       | 관리자 비밀번호 입력    |
    | 암호 확인               | 관리자 비밀번호 다시 입력 |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="가상 시스템 규모 집합을 만듭니다." border="true":::

4. 네트워킹 탭을 **선택합니다.**

5. **네트워킹** 탭에서 이 정보를 입력하거나 선택합니다.

     설정                           | 값                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Virtual Network 구성** |                                                          |
    | 가상 네트워크                   | **myVNet** 또는 기존 가상 네트워크를 선택합니다.      |
    | **부하 분산**                |                                                          |
    | 로드 밸러블러 사용               | **예**를 선택합니다.                                           |
    | **부하 분산 설정**       |                                                          |
    | 부하 분산 옵션            | **Azure 로드 밸러울러** 선택                           |
    | 로드 밸러블러 선택            | **myLoad밸슬러** 또는 기존 로드 밸러블러 선택 |
    | 백 엔드 풀 선택             | **myBackendPool** 또는 기존 백 엔드 풀을 선택합니다.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="가상 시스템 규모 집합을 만듭니다." border="true":::

6. 관리 탭을 **선택합니다.**

7. **관리** 탭에서 부팅 진단을 **해제로** **설정합니다.**

8. 파란색 **검토 + 만들기** 버튼을 선택합니다.

9. 설정을 검토하고 **만들기** 단추를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 기존 Azure 로드 밸런서가 있는 가상 시스템 규모 집합을 배포했습니다.  가상 시스템 스케일 세트 및 로드 밸러블러에 대한 자세한 내용은 다음을 참조하십시오.

- [Azure Load Balancer란?](load-balancer-overview.md)
- [가상 머신 크기 집합이란?](../virtual-machine-scale-sets/overview.md)
