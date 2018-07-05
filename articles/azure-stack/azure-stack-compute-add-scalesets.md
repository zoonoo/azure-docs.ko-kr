---
title: Virtual Machine Scale Sets를 Azure Stack에서 사용할 수 있도록 | Microsoft Docs
description: 클라우드 운영자가 Azure Stack Marketplace에 Virtual Machine Scale Sets을 추가할 수는 하는 방법에 대해 알아봅니다.
services: azure-stack
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 06/05/2018
ms.author: brenduns
ms.reviewer: kivenkat
ms.openlocfilehash: ddde2e6bad8a373df405ac05e78a5dbccd0257fc
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2018
ms.locfileid: "34800643"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Azure Stack에서 Virtual Machine Scale Sets 사용 가능

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

가상 머신 확장 집합은 Azure Stack 계산 리소스입니다. 배포 하 고 동일한 가상 머신 집합을 관리 하 사용할 수 있습니다. 모든 가상 머신과 동일 하 게 구성, 확장 집합 가상 머신의 미리 프로 비전 필요 하지 않습니다. 큰 계산, 빅 데이터 및 컨테이너 화 된 워크 로드를 대상으로 하는 대규모 서비스를 빌드하는 것이 쉽습니다.

이 문서에서는 Azure Stack Marketplace에서 확장 집합을 사용할 수 있도록 하는 프로세스를 안내 합니다. 이 절차를 완료 하면 해당 구독에 가상 머신 확장 집합에 사용자를 추가할 수 있습니다.

Azure Stack에 가상 머신 확장 집합은 Azure에서 가상 머신 확장 집합 같습니다. 자세한 내용은 다음 비디오를 참조 하세요.
* [Mark Russinovich의 Azure 확장 집합 설명](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Guy Bowerman과 Virtual Machine Scale Sets](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Azure Stack에 가상 머신 확장 집합 자동 크기 조정을 지원 하지 않습니다. 확장 Resource Manager 템플릿, CLI 또는 PowerShell을 사용 하 여 집합에 더 많은 인스턴스를 추가할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- **마켓플레이스 배포**  
    Azure Stack Marketplace 배포를 사용 하도록 설정 하려면 전역 Azure를 사용 하 여 등록 합니다. 지침을 따릅니다 [Azure 사용 하 여 Azure Stack 등록](azure-stack-registration.md)합니다.
- **운영 체제 이미지**  
    Azure Stack Marketplace에는 운영 체제 이미지를 추가 하지 않은 [Azure에서 Azure Stack marketplace 항목 추가](asdk/asdk-marketplace-item.md)합니다.

## <a name="add-the-virtual-machine-scale-set"></a>가상 머신 확장 집합을 추가 합니다.

1. Azure Stack Marketplace를 열고 Azure에 연결 합니다. 선택 **Marketplace management**> **+ Azure에서 추가**합니다.

    ![Marketplace 관리](media/azure-stack-compute-add-scalesets/image01.png)

2. 추가한 가상 머신 확장 집합 마켓플레이스 항목을 다운로드 합니다.

    ![가상 머신 확장 집합](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>가상 머신 확장 집합의 이미지를 업데이트 합니다.

가상 머신 확장 집합을 만든 후 사용자는 확장 집합 다시 생성 하지 않아도 없이 확장 집합의 이미지를 업데이트할 수 있습니다. 이미지를 업데이트 하는 프로세스는 다음과 같은 시나리오에 따라 달라 집니다.

1. 가상 머신 확장 집합 배포 템플릿에 **최신 지정** 에 대 한 *버전*:  

   경우는 *버전* 으로 설정 됩니다 **최신** 에 *imageReference* 확장에 대 한 템플릿의 섹션 설정, 사용 가능한 최신 버전을 사용 하는 확장 집합에 대 한 작업을 강화 눈금에 대 한 이미지의 인스턴스를 설정 합니다. 수직 확장을 완료 되 면 이전 가상 머신 확장 집합 인스턴스를 삭제할 수 있습니다.  (값 *게시자*를 *제공*, 및 *sku* 그대로 유지). 

   다음은 지정 하는 예가 *최신*:  

    ```Json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   확장에서 새 이미지를 사용 하려면 먼저 새 이미지를 다운로드 해야 합니다.  

   - Marketplace에서 이미지 경우 확장 집합의 이미지 보다 최신 버전: 이전 이미지를 대체 하는 새 이미지를 다운로드 합니다. 이미지를 교체한 후 사용자는 확장을 진행할 수 있습니다. 

   - Marketplace에서 이미지 버전 경우 확장 집합의 이미지와 동일: 확장 집합에서 사용 되는 이미지를 삭제 한 후 새 이미지를 다운로드 합니다. 원본 이미지를 제거 하 고 새 이미지 다운로드 사이의 시간 동안 확장할 수 없습니다. 
      
     이 프로세스는 필요한 resyndicate 하려면 하는 이미지 버전 1803 도입 된, 스파스 파일 형식의 사용 합니다. 
 

2. 가상 머신 확장 집합 배포 템플릿을 **최신 지정 하지 않습니다** 에 대 한 *버전* 대신 버전 번호를 지정 합니다.  

    최신 버전 (변경 하는 사용 가능한 버전)를 사용 하 여 이미지를 다운로드 하는 경우 확장 집합 확장할 수 없습니다. 이 의도적인 확장 집합 템플릿에서 지정 된 이미지 버전을 사용할 수 있어야 합니다.  

자세한 내용은 [운영 체제 디스크 및 이미지](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images)합니다.  


## <a name="remove-a-virtual-machine-scale-set"></a>가상 머신 확장 집합을 제거 합니다.

가상 컴퓨터를 제거 하 갤러리 항목 집합 크기 조정, 다음 PowerShell 명령을 실행 합니다.

```PowerShell  
    Remove-AzsGalleryItem
````

> [!NOTE]
> 갤러리 항목을 즉시 제거할 수 있습니다. 포털을 새로 고치려면 여러 번 Marketplace에서 제거 된 항목 표시 하기 전에 해야 할 야간 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure Stack에 대 한 질문과 대답](azure-stack-faq.md)