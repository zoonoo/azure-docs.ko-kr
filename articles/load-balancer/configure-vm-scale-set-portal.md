---
title: 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합 구성-Azure Portal
description: Azure Portal를 사용 하 여 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합을 구성 하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: e12532107ed554385ccf38531bb95b7b70298934
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333888"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Azure Portal를 사용 하 여 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합 구성

이 문서에서는 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합을 구성 하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독
- 가상 머신 확장 집합이 배포 될 구독의 기존 표준 sku 부하 분산 장치입니다.
- 가상 머신 확장 집합에 대 한 Azure Virtual Network입니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>기존 부하 분산 장치를 사용 하 여 가상 머신 확장 집합 배포

이 섹션에서는 기존 Azure 부하 분산 장치를 사용 하 여 Azure Portal에서 가상 머신 확장 집합을 만듭니다.

> [!NOTE]
> 다음 단계에서는 **Myvnet** 이라는 가상 네트워크와 **Myvnet** 라는 Azure 부하 분산 장치를 이전에 배포 했다고 가정 합니다.

1. 화면 왼쪽 상단에서 **리소스 만들기**  >  **계산**  >  **가상 머신 확장 집합** 을 클릭 하거나 marketplace 검색에서 **가상 머신 확장 집합** 을 검색 합니다.

2. **만들기**를 선택합니다.

3. **가상 머신 확장 집합 만들기**에서를 입력 하거나 **기본** 설정 탭에서이 정보를 선택 합니다.

    | 설정                        | 값                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **프로젝트 세부 정보**            |                                                                                                       |
    | Subscription                   | Azure 구독 선택                                                                        |
    | 리소스 그룹                 | 새로 만들기를 선택 하 고 **Myresourcegroup**을 입력 한 다음 확인을 선택 하거나 기존 리소스 그룹을 선택 합니다. |
    | **확장 집합 정보**          |                                                                                                       |
    | 가상 머신 확장 집합 이름 | **Myvmss** 를 입력 합니다.                                                                                      |
    | 지역                         | **미국 동부 2** 를 선택 합니다.                                                                                    |
    | 가용성 영역              | **없음**을 선택합니다.                                                                                       |
    | **인스턴스 세부 정보**           |                                                                                                       |
    | 이미지                          | **Ubuntu Server 18.04 LTS** 를 선택 합니다.                                                                    |
    | Azure Spot 인스턴스            | **아니요**를 선택합니다.                                                                                         |
    | 크기                           | 기본값 유지                                                                                      |
    | **관리자 계정**      |                                                                                                       |
    | 인증 유형            | **암호** 선택                                                                                   |
    | 사용자 이름                       | 관리자 사용자 이름 입력        |
    | 암호                       | 관리자 암호 입력    |
    | 암호 확인               | 관리자 암호를 다시 입력 합니다. |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="가상 머신 확장 집합을 만듭니다." border="true":::

4. **네트워킹** 탭을 선택합니다.

5. **네트워킹** 탭에서 다음 정보를 입력 하거나 선택 합니다.

     설정                           | 값                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Virtual Network 구성** |                                                          |
    | 가상 네트워크                   | **Myvnet** 또는 기존 가상 네트워크를 선택 합니다.      |
    | **부하 분산**.                |                                                          |
    | 부하 분산 장치 사용               | **예**를 선택합니다.                                           |
    | **부하 분산 설정**       |                                                          |
    | 부하 분산 옵션            | **Azure 부하 분산 장치** 선택                           |
    | 부하 분산 장치 선택            | **Myloadbalancer** 또는 기존 부하 분산 장치를 선택 합니다. |
    | 백 엔드 풀 선택             | **MyBackendPool** 또는 기존 백 엔드 풀을 선택 합니다.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="가상 머신 확장 집합을 만듭니다." border="true":::

6. **관리** 탭을 선택 합니다.

7. **관리** 탭에서 **부팅 진단** 을 **꺼짐**으로 설정 합니다.

8. 파란색 **검토 + 만들기** 단추를 선택 합니다.

9. 설정을 검토 하 고 **만들기** 단추를 선택 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합을 배포 했습니다.  가상 머신 확장 집합 및 부하 분산 장치에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure Load Balancer란?](load-balancer-overview.md)
- [가상 머신 크기 집합이란?](../virtual-machine-scale-sets/overview.md)
