---
title: 서비스 엔드포인트 정책 만들기 및 연결 - Azure Portal
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure Portal을 사용하여 서비스 엔드포인트 정책을 설정 및 연결하는 방법에 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: d26fd2fec5f9d5ab8e9d82ff2c6bd83b11c72e99
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651277"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Azure Portal을 사용하여 서비스 엔드포인트를 만들기, 변경 또는 삭제

서비스 엔드포인트 정책을 사용하면 서비스 엔드포인트를 통해 특정 Azure 리소스에 가상 네트워크 트래픽을 필터링 할 수 있습니다. 서비스 엔드포인트 정책을 잘 모르는 경우 [서비스 엔드포인트 정책 개요](virtual-network-service-endpoint-policies-overview.md)를 참조하여 자세히 알아보세요.

 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 서비스 엔드포인트 정책 만들기
> * 서비스 엔드포인트 정책 정의 만들기
> * 서브넷을 사용하여 가상 네트워크 만들기
> * 서비스 엔드포인트 정책을 서브넷에 연결

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인 

[https://portal.azure.com](https://portal.azure.com) 에서 Azure Portal에 로그인합니다.

## <a name="create-a-service-endpoint-policy"></a>서비스 엔드포인트 정책 만들기

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다.
2. 검색 창에서 "서비스 끝점 정책"을 입력 하 고 **서비스 끝점 정책** 을 선택한 후 **만들기**를 선택 합니다.

![서비스 끝점 정책 만들기](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. **기본**에 다음 정보를 입력하거나 선택 

   - 구독: 정책에 대 한 구독을 선택 합니다.
   - 리소스 그룹: **새로 만들기** 를 선택 하 고 *myresourcegroup* 을 입력 합니다.
   - 이름: myEndpointPolicy
   - 위치: 미국 중부
 
   ![서비스 엔드포인트 정책 기본 만들기](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. **리소스** 에서 **+ 추가** 를 선택 하 고 **리소스 추가** 창에서 다음 정보를 입력 하거나 선택 합니다.

   - 서비스: 서비스 끝점 정책에는 **Microsoft 저장소** 만 사용할 수 있습니다.
   - 범위: **단일 계정**에서 하나를 선택 하 고 **구독의 모든 계정** 및 **리소스 그룹의 모든 계정** 을 선택 합니다.
   - 구독: 스토리지 계정에 대한 구독을 선택합니다. 정책 및 스토리지 계정은 다른 구독에 있을 수 있습니다.
   - 리소스 그룹: 리소스 그룹을 선택합니다. 범위가 "리소스 그룹의 모든 계정" 또는 "단일 계정"으로 설정된 경우 필요합니다.  
   - 리소스: 선택한 구독 또는 리소스 그룹에서 Azure Storage 리소스를 선택 합니다.
   - 아래쪽의 **추가** 단추를 클릭 하 여 리소스 추가를 마칩니다.

   ![서비스 끝점 정책 정의-리소스](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - 필요에 따라 위의 단계를 반복 하 여 더 많은 리소스를 추가 합니다.

5. 선택 사항: **태그**에 다음 정보를 입력하거나 선택합니다.
   
   - 키: 정책에 대한 키를 선택합니다. 예: 부서     
   - 값: 키에 대한 값 쌍을 입력합니다. 예: 금융

6. **검토 + 만들기**를 선택합니다. 정보의 유효성을 검사하고 **만들기**를 클릭합니다. 추가로 편집하려면 **이전**을 클릭합니다. 

   ![서비스 엔드포인트 정책 최종 유효성 검사 만들기](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>엔드포인트 정책 보기 

1. 포털의 *모든 서비스* 상자에 *서비스 엔드포인트 정책* 입력을 시작합니다. **서비스 끝점 정책**을 선택 합니다.
2. 다음 그림에 표시된 것처럼 **구독**에서 구독 및 리소스 그룹 선택

   ![정책 표시](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. 더 많은 정책 정의를 보거나 추가하려면 정책을 선택하고 **정책 정의**를 클릭합니다.

   ![정책 정의 표시](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. **연결된 서브넷**을 선택하여 정책이 연결된 서브넷을 확인합니다. 서브넷이 아직 연결 되지 않은 경우 다음 단계의 지침을 따르세요.

   ![연결 된 서브넷](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. 서브넷에 정책 연결

>[!WARNING] 
> 정책을 지정 된 서브넷에 연결 하기 전에 서브넷에서 액세스 한 모든 리소스를 정책 정의에 추가 해야 합니다. 정책이 연결 되 면 나열 된 리소스 *허용* 에 대 한 액세스만 서비스 끝점에서 허용 됩니다. 
>
> 또한 서비스 끝점 정책에 연결 된 서브넷에 관리 되는 Azure 서비스가 없어야 합니다.

- 서브넷에 정책을 연결 하려면 먼저 가상 네트워크 및 서브넷을 만들어야 합니다. 이에 대 한 도움말은 [Virtual Network 만들기](./quick-create-portal.md) 문서를 참조 하세요.

- 가상 네트워크 및 서브넷을 설정한 후에는 Azure Storage에 대 한 Virtual Network 서비스 끝점을 구성 해야 합니다. Virtual Network 블레이드에서 **서비스 끝점**을 선택 하 고 다음 창에서 **Microsoft. 저장소** 를 선택 하 고 **서브넷** 에서 원하는 VNet 또는 서브넷을 선택 합니다.

- 이제 아래와 같이 서브넷에 대 한 서비스 끝점을 구성 하기 전에 서비스 끝점 정책을 이미 만든 경우 위의 창에서 서비스 끝점 정책을 선택 하도록 선택할 수 있습니다.

    ![서비스 끝점을 만드는 동안 서브넷 연결](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- 또는 서비스 끝점이 이미 구성 된 후 서비스 끝점 정책을 연결 하는 경우 아래와 같이 **연결 된 서브넷** 창으로 이동 하 여 서비스 끝점 정책 블레이드 내에서 서브넷을 연결 하도록 선택할 수 있습니다.

    ![9 월을 통해 서브넷 연결](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>모든 지역의 Azure Storage 리소스에 대 한 액세스는이 서브넷에서 서비스 끝점 정책에 따라 제한 됩니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 서비스 엔드포인트 정책을 만들어 서브넷에 연결했습니다. 서비스 엔드포인트 정책에 대한 자세한 내용은 [서비스 엔드포인트 정책 개요](virtual-network-service-endpoint-policies-overview.md)를 참조하세요.
