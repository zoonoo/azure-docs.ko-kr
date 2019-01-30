---
title: Virtual Machine Scale Sets를 Azure Stack에서 사용할 수 있도록 | Microsoft Docs
description: 클라우드 운영자가 Azure Stack Marketplace에 Virtual Machine Scale Sets을 추가할 수는 하는 방법에 대해 알아봅니다.
services: azure-stack
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 10/22/2018
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 3f1c84961f2ad6bd15612917d33982ec96824257
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252271"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Azure Stack에서 Virtual Machine Scale Sets 사용 가능

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*
  
가상 머신 확장 집합은 Azure Stack 계산 리소스입니다. 배포 하 고 동일한 가상 머신 집합을 관리 하 사용할 수 있습니다. 동일한 방식으로 구성 된 모든 가상 컴퓨터를 사용 하 여 확장 집합 않아도 가상 머신의 미리 프로 비전 됩니다. 큰 계산, 빅 데이터 및 컨테이너 화 된 워크 로드를 대상으로 하는 대규모 서비스를 빌드하는 것이 쉽습니다.

이 문서에서는 Azure Stack Marketplace에서 확장 집합을 사용할 수 있도록 하는 프로세스를 안내 합니다. 이 절차를 완료 하면 해당 구독에 가상 머신 확장 집합에 사용자를 추가할 수 있습니다.

Azure Stack에 가상 머신 확장 집합 Azure에서 가상 머신 확장 집합와 비슷합니다. 자세한 내용은 다음 비디오를 참조 하세요.
* [Mark Russinovich의 Azure 확장 집합 설명](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Guy Bowerman과 Virtual Machine Scale Sets](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Azure Stack에 가상 머신 확장 집합 자동 크기 조정을 지원 하지 않습니다. 확장 Resource Manager 템플릿, CLI 또는 PowerShell을 사용 하 여 집합에 더 많은 인스턴스를 추가할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- **Marketplace.** Marketplace에서 항목의 가용성을 사용 하도록 설정 하려면 전역 Azure를 사용 하 여 Azure Stack을 등록 합니다. 지침을 따릅니다 [Azure 사용 하 여 Azure Stack 등록](azure-stack-registration.md)합니다.
- **운영 체제 이미지:** 가상 머신 확장 집합 (VMSS)을 만들 수 있습니다, 전에에서 VMSS에서 사용할 VM 이미지를 다운로드 해야 합니다 [Azure Stack Marketplace](azure-stack-download-azure-marketplace-item.md)합니다. 이미지를 이미 있어야 사용자는 새 VMSS를 만들 수 있습니다. 

## <a name="use-the-azure-stack-portal"></a>Azure Stack 포털 사용 

>[!IMPORTANT]  
> 이 섹션의 정보에는 Azure Stack 1808 이후 버전을 사용 하는 경우 적용 됩니다. 버전 1807 이하의 경우 참조 [1808) (이전 가상 머신 확장 집합 추가](#add-the-virtual-machine-scale-set-prior-to-version-1808)합니다.

1. Azure Stack 포털에 로그인 합니다. 그런 다음 **모든 서비스**, 다음 **가상 머신 확장 집합**를 선택한 후 **계산**를 선택 **가상 머신 확장 집합**. 
   ![선택 된 가상 머신 확장 집합](media/azure-stack-compute-add-scalesets/all-services.png)

2. 만들기를 선택 ***가상 머신 확장 집합***합니다.
   ![가상 머신 확장 집합 만들기](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. 빈 필드를 채우고에 대 한 드롭다운 목록에서 선택 **운영 체제 디스크 이미지**하십시오 **구독**, 및 **인스턴스 크기**. 선택 **Yes** 에 대 한 **managed disks 사용**합니다. 그런 다음에 **만들기**를 클릭합니다.
    ![구성 및 만들기](media/azure-stack-compute-add-scalesets/create.png)

4. 새 가상 머신 확장 집합로 이동 하십시오 **모든 리소스**, 가상 머신 확장 집합 이름, 검색 및 검색에서 해당 이름을 클릭 합니다. 
   ![확장 집합을 보려면](media/azure-stack-compute-add-scalesets/search.png)

## <a name="add-the-virtual-machine-scale-set-prior-to-version-1808"></a>(이전 버전 1808) 가상 머신 확장 집합 추가

>[!IMPORTANT]  
> 이 섹션의 정보에는 Azure Stack의 1808 이전 버전을 사용 하는 경우 적용 됩니다. 1808 이상 버전을 사용 하는 경우 참조 [Azure Stack 포털을 사용 하 여](#use-the-azure-stack-portal)입니다.

1. Azure Stack Marketplace를 열고 Azure에 연결 합니다. 선택 **Marketplace management**, 클릭 **+ Azure에서 추가**합니다.

    ![Marketplace 관리](media/azure-stack-compute-add-scalesets/image01.png)

2. 추가한 가상 머신 확장 집합 마켓플레이스 항목을 다운로드 합니다.

    ![가상 머신 확장 집합](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>가상 머신 확장 집합의 이미지를 업데이트 합니다.

가상 머신 확장 집합을 만든 후 사용자는 확장 집합 다시 생성 하지 않아도 없이 확장 집합의 이미지를 업데이트할 수 있습니다. 이미지를 업데이트 하는 프로세스는 다음과 같은 시나리오에 따라 달라 집니다.

1. 가상 머신 확장 집합 배포 템플릿을 지정 **최신** 에 대 한 **버전**:  

   경우는 `version` 로 설정 된 **최신** 에 `imageReference` 확장에 대 한 템플릿의 섹션 설정, 크기 조정 확장 집합 사용에 대 한 작업을 이미지의 사용 가능한 최신 버전의 확장 집합 인스턴스. 수직 확장 완료 되 면 이전 가상 머신 확장 집합 인스턴스를 삭제할 수 있습니다. 에 대 한 값 `publisher`, `offer`, 및 `sku` 그대로 유지 됩니다. 

   다음 JSON 예제 지정 `latest`:  

    ```json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   확장에서 새 이미지를 사용 하려면 먼저 새 이미지를 다운로드 해야 합니다.  

   - 이미지 Marketplace에서 확장 집합의 이미지 보다 최신 버전 경우 이전 이미지를 대체 하는 새 이미지를 다운로드 합니다. 이미지를 교체한 후 사용자는 확장을 진행할 수 있습니다. 

   - Marketplace에서 이미지 버전을 확장 집합의 이미지와 동일한 경우 확장 집합에서 사용 되는 이미지를 삭제 하 고 새 이미지를 다운로드 합니다. 원본 이미지를 제거 하 고 새 이미지 다운로드 사이의 시간 동안 확장할 수 없습니다. 
      
     이 프로세스는 필요한 resyndicate 하려면 하는 이미지 버전 1803 도입 된, 스파스 파일 형식의 사용 합니다. 
 
2. 가상 머신 확장 집합 배포 템플릿을 **최신 지정 하지 않습니다** 에 대 한 **버전** 대신 버전 번호를 지정 합니다.  

    최신 버전 (변경 하는 사용 가능한 버전)를 사용 하 여 이미지를 다운로드 하는 경우 확장 집합 확장할 수 없습니다. 이 기본적으로 확장 집합 템플릿에서 지정 된 이미지 버전을 사용할 수 있어야 합니다.  

자세한 내용은 [운영 체제 디스크 및 이미지](./user/azure-stack-compute-overview.md#operating-system-disks-and-images)합니다.  

## <a name="scale-a-virtual-machine-scale-set"></a>가상 머신 확장 집합 확장

크기를 조정할 수는 *가상 머신 확장 집합* 크게 또는 작게 되도록 합니다.  

1. 포털의 확장 집합을 선택 하 고 선택한 **크기 조정**합니다.

2. 슬라이드 막대를 사용 하 여이 가상 머신 확장 집합에 대 한 크기 조정에 대 한 새로운 수준의 설정 하 고 클릭 **저장할**합니다.
     ![확장 집합](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="remove-a-virtual-machine-scale-set"></a>가상 머신 확장 집합을 제거 합니다.

Virtual Machine Scale Set 갤러리 항목을 제거 하려면 다음 PowerShell 명령을 실행 합니다.

```PowerShell  
Remove-AzsGalleryItem
```

> [!NOTE]
> 갤러리 항목을 즉시 제거할 수 있습니다. 포털을 새로 고치려면 여러 번 Marketplace에서 제거 된 항목 표시 하기 전에 해야 할 야간 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure에서 Azure Stack marketplace 항목 다운로드](azure-stack-download-azure-marketplace-item.md)