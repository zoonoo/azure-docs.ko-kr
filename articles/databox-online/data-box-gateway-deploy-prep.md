---
title: Data Box Gateway를 배포하기 위해 Azure Portal을 준비하는 방법에 대한 자습서 | Microsoft Docs
description: Azure Data Box Gateway를 배포하는 첫 번째 자습서에는 Azure Portal 준비가 포함됩니다.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: b8aac8a6a666bf3f360ef2867ff15933ad72dbd7
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166987"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway-preview"></a>자습서: Azure Data Box Gateway 배포 준비(미리 보기)


이 문서는 Azure Data Box Gateway를 완전히 배포하는 데 필요한 배포 자습서 시리즈 중 첫 번째 자습서입니다. 이 자습서에서는 Data Box Gateway 리소스를 배포하기 위해 Azure Portal을 준비하는 방법을 설명합니다. 

설치 및 구성 프로세스를 완료하려면 관리자 권한이 필요합니다. 포털 준비에는 10분 미만이 소요됩니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 새 리소스 만들기
> * 가상 장치 이미지 다운로드
> * 활성화 키 가져오기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.


> [!IMPORTANT]
> - Data Box Gateway는 미리 보기로 제공되고 있습니다. 이 솔루션을 주문하고 배포하기 전에 [미리 보기에 대한 Azure 서비스 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 검토하세요. 

### <a name="get-started"></a>시작하기

Data Box Gateway를 배포하려면 다음 자습서를 규정된 순서대로 참조하세요.

| **#** | **단계** | **이러한 문서를 사용합니다.** |
| --- | --- | --- | 
| 1. |**[Data Box Gateway를 위해 Azure Portal 준비](data-box-gateway-deploy-prep.md)** |Data Box Gateway 가상 장치를 프로비전하기 전에 Data Box Gateway 리소스를 만들고 구성합니다. |
| 2. |**[Hyper-V에서 Data Box Gateway 프로비전](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[VMware에서 Data Box Gateway 프로비전](data-box-gateway-deploy-provision-vmware.md)**|Hyper-V의 경우 Windows Server 2016이나 Windows Server 2012 R2에서 Hyper-V를 실행하는 호스트 시스템에서 Data Box Gateway 가상 장치를 프로비전하고 연결합니다. <br><br><br> VMware의 경우, VMware ESXi 6.0 또는 6.5를 실행하는 호스트 시스템에서 Data Box Gateway 가상 장치에 프로비전하고 연결합니다.<br></br> |
| 3. |**[Data Box Gateway 연결, 설정, 활성화](data-box-gateway-deploy-connect-setup-activate.md)** |로컬 웹 UI에 연결하고 장치 설정을 완료한 다음, 장치를 활성화합니다. 그런 다음 SMB 공유를 프로비전할 수 있습니다.  |
| 4. |**[Data Box Gateway를 통해 데이터 전송](data-box-gateway-deploy-add-shares.md)** |공유를 추가하고, SMB 또는 NFS를 통해 공유에 연결합니다. |

이제 Azure Portal 설치를 시작할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

여기에는 Data Box Gateway 리소스, Data Box Gateway 장치 및 데이터 센터 네트워크에 대한 구성 필수 조건이 있습니다.

### <a name="for-the-data-box-gateway-resource"></a>Data Box Gateway 리소스의 경우

시작하기 전에 다음 사항을 확인합니다.

* Data Box Gateway 리소스에 대해 Microsoft Azure 구독을 사용하도록 설정해야 합니다.
* 액세스 자격 증명이 있는 Microsoft Azure 저장소 계정이 있습니다.

### <a name="for-the-data-box-gateway-device"></a>Data Box Gateway 장치의 경우

가상 장치를 배포하기 전에 다음 사항을 확인해야 합니다.

* 장치 프로비전에 사용될 수 있는 Windows Server 2012 R2 이상 또는 VMware(ESXi 6.0 또는 6.5)에서 Hyper-V를 실행하는 호스트 시스템에 액세스할 수 있습니다.
* 호스트 시스템은 다음 리소스를 전용하여 Data Box 가상 장치를 프로비전할 수 있습니다.
  
  * 코어 4개 이상
  * RAM 8GB 이상 
  * 네트워크 인터페이스 하나
  * 250GB OS 디스크
  * 시스템 데이터용 가상 디스크 2TB

### <a name="for-the-datacenter-network"></a>데이터 센터 네트워크의 경우

시작하기 전에 다음 사항을 확인합니다.

* 데이터 센터의 네트워크가 Data Box Gateway 장치에 대한 네트워킹 요구 사항에 따라 구성되어 있습니다. 자세한 내용은 [Data Box Gateway 시스템 요구 사항](data-box-gateway-system-requirements.md)을 참조하세요.

* Data Box Gateway에는 항상 사용 가능한 전용 20MB 인터넷 대역폭(또는 이상)이 있습니다. 이 대역폭은 다른 응용 프로그램과 공유하면 안됩니다. 네트워크 제한을 사용하는 경우 제한이 작동하려면 32Mbps 인터넷 대역폭 이상을 사용하는 것이 좋습니다.

## <a name="create-a-new-resource"></a>새 리소스 만들기

새 Data Box Gateway 리소스를 만들려면 다음 단계를 수행합니다. 

가상 장치를 관리할 기존 Data Box Gateway 리소스가 있으면 이 단계를 건너뛰고 [활성화 키 가져오기](#get-the-activation-key)로 이동합니다.

Data Box 리소스를 만들려면 Azure Portal에서 다음 단계를 수행합니다.
1. Microsoft Azure 자격 증명을 사용하여 [https://aka.ms/databox-edge](https://aka.ms/databox-edge) URL에서 Azure Preview 포털에 로그인합니다. 

2. Data Box Edge 미리 보기에서 사용하려는 구독을 선택합니다. Data Box Edge 리소스를 배포할 지역을 선택합니다. **Data Box Gateway** 옵션에서 **만들기**를 클릭합니다.

    ![Data Box Gateway 서비스 검색](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

3. 새 리소스를 보려면 다음 정보를 입력하거나 선택합니다.
    
    |설정  |값  |
    |---------|---------|
    |리소스 이름   | 리소스를 식별하기 위한 이름.<br>이름에는 문자, 숫자 및 하이픈을 포함하여 2~50자가 포함됩니다.<br> 이름은 문자 또는 숫자로 시작하고 끝납니다.        |
    |구독    |구독은 청구 계정에 연결됩니다. |
    |리소스 그룹  |기존 그룹을 선택하거나 새 그룹을 만듭니다.<br>[Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)에 대해 자세히 알아봅니다.     |
    |위치     |이 릴리스에서는 미국 동부, 미국 서부 2, 동남 아시아 및 유럽 서부를 사용할 수 있습니다. <br> 장치를 배포하려는 지역에 지리적으로 가장 가까운 위치를 선택합니다.|
    
    ![Data Box 게이트웨이 리소스 만들기](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
4. **확인**을 클릭합니다.
 
리소스 생성에는 몇 분 정도가 소요됩니다. 리소스가 성공적으로 만들어진 후 적절한 알림이 표시됩니다.


## <a name="download-the-virtual-device-image"></a>가상 장치 이미지 다운로드

Data Box Gateway가 만들어진 후에는 적절한 가상 장치 이미지를 다운로드하여 호스트 시스템에 가상 장치를 프로비전합니다. 가상 장치 이미지는 운영 체제 별로 구분되며 Azure Portal에서 리소스의 **빠른 시작** 블레이드에서 다운로드할 수 있습니다.

> [!IMPORTANT]
> Data Box Gateway에서 실행되는 소프트웨어는 Data Box Gateway 리소스에서만 사용할 수 있습니다.


[Azure Portal](https://portal.azure.com/)에서 다음 단계를 수행합니다.

1. 사용자가 만든 리소스를 클릭한 다음, **개요**를 클릭합니다. 기존 Azure Data Box Gateway 리소스가 있는 경우 리소스를 클릭하고 **개요**로 이동합니다.

    ![새로운 Data Box 게이트웨이 리소스](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

4. 오른쪽 창의 빠른 시작에서 다운로드하려는 이미지에 해당하는 링크를 클릭합니다. 이미지 파일은 약 4.8GB입니다.
   
   * [Windows Server 2012 R2 이상의 Hyper-V용 VHDX](https://aka.ms/dbe-vhdx-2012).
   * [VMWare ESXi 6.0 또는 6.5용 VMDK](https://aka.ms/dbe-vmdk).

5. 파일을 다운로드하고 로컬 드라이브에 압축을 푼 다음 압축을 푼 위치를 적어둡니다.


## <a name="get-the-activation-key"></a>활성화 키 가져오기

Data Box Gateway 리소스가 가동하여 실행되면 활성화 키를 얻어야 합니다. 이 키는 Data Box Gateway 장치를 활성화하고 리소스와 연결하는 데 사용됩니다.

활성화 키는 Data Box Gateway 리소스와 함께 활성화해야 하는 모든 Data Box Gateway 장치를 등록하는 데 사용됩니다. 이제 Azure Portal에서 이 키를 얻을 수 있습니다.

1. 사용자가 만든 리소스를 클릭한 다음, **개요**를 클릭합니다.

    ![새로운 Data Box 게이트웨이 리소스](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. **키 생성**을 클릭하여 활성화 키를 만듭니다. 복사 아이콘을 클릭하여 키를 복사한 다음, 나중에 사용할 수 있도록 저장합니다.

    ![활성화 키 가져오기](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - 활성화 키는 생성되고 3일 후에 만료됩니다. 
> - 키가 만료되면 새 키를 생성하세요. 이전 키는 유효하지 않습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 Data Box Gateway 토픽에 대해 알아보았습니다.

> [!div class="checklist"]
> * 새 리소스 만들기
> * 가상 장치 이미지 다운로드
> * 활성화 키 가져오기

Data Box Gateway를 위한 가상 머신을 프로비전하는 방법을 알아보려면 다음 자습서로 계속 진행하세요. 호스트 운영 체제에 따라서 다음의 자세한 지침을 참조하세요.

> [!div class="nextstepaction"]
> [Hyper-V에서 Data Box Gateway 프로비전](./data-box-gateway-deploy-provision-hyperv.md)

또는

> [!div class="nextstepaction"]
> [VMware에서 Data Box Gateway 프로비전](./data-box-gateway-deploy-provision-vmware.md)


