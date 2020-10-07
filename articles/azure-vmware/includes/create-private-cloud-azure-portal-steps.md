---
title: Azure VMware Solution 배포
description: Azure Portal을 사용하여 Azure VMware Solution을 배포하는 단계입니다.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 4d0881721cd8c13d1b6c9fb3a29e4cdb6d6a753f
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578321"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **새 리소스 만들기**를 선택합니다. **Marketplace 검색** 텍스트 상자에서 `Azure VMware Solution`을 입력하고, 목록에서 **Azure VMware 솔루션**을 선택합니다. **Azure VMware Solution** 창에서 **만들기**를 선택합니다.

1. **기본** 탭에서 필드 값을 입력합니다. 다음 표에서는 필드에 대한 속성을 나열합니다.

   | 필드   | 값  |
   | ---| --- |
   | **구독** | 배포에 사용할 구독입니다.|
   | **리소스 그룹** | 프라이빗 클라우드 리소스에 대한 리소스 그룹입니다. |
   | **위치** | 위치(예: **미국 동부**)를 선택합니다.|
   | **리소스 이름** | Azure VMware Solution 프라이빗 클라우드의 이름입니다. |
   | **SKU** | 다음 SKU 값을 선택합니다. AV36 |
   | **호스트** | 프라이빗 클라우드 클러스터에 추가할 호스트의 수입니다. 기본값은 3이며, 배포 후에 늘리거나 줄일 수 있습니다.  |
   | **vCenter 관리자 암호** | 클라우드 관리자 암호를 입력합니다. |
   | **NSX-T 관리자 암호** | NSX-T 관리자 암호를 입력합니다. |
   | **주소 블록** | 프라이빗 클라우드의 CIDR 네트워크에 대한 IP 주소 블록(예: 10.175.0.0/22)을 입력합니다. |
   | **Virtual Network** | Virtual Network를 선택하거나 Azure VMware Solution 프라이빗 클라우드에 대한 새 항목을 만듭니다.  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="기본 탭에서 필드 값을 입력합니다." border="true":::

1. 완료되면 **검토 + 만들기**를 선택합니다. 다음 화면에서 입력한 정보를 확인합니다. 정보가 모두 올바르면 **만들기**를 선택합니다.

   > [!NOTE]
   > 이 단계는 약 두 시간 정도 걸립니다. 

1. 배포가 성공했는지 확인합니다. 만든 리소스 그룹으로 이동하고 프라이빗 클라우드를 선택합니다.  배포가 완료되면 **성공** 상태가 표시됩니다. 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="기본 탭에서 필드 값을 입력합니다." border="true":::