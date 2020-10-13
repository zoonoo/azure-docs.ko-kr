---
title: Azure Portal에서 GPU가 있는 Azure Stack Edge Pro 디바이스를 활성화하는 자습서 | Microsoft Docs
description: Azure Stack Edge Pro GPU를 배포하기 위한 자습서에서는 물리적 디바이스를 활성화하는 방법을 안내합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 6807174f374e75f826001afecc1eccf8ba1a0603
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743389"
---
# <a name="tutorial-activate-azure-stack-edge-pro-with-gpu"></a>자습서: GPU가 있는 Azure Stack Edge Pro 활성화

이 자습서에서는 로컬 웹 UI를 사용하여 온보드 GPU가 있는 Azure Stack Edge Pro 디바이스를 활성화하는 방법을 설명합니다.

활성화 프로세스를 완료하는 데 5분 정도가 소요됩니다.

이 자습서에서는 다음에 대해 알아보았습니다.

> [!div class="checklist"]
> * 사전 요구 사항
> * 물리적 디바이스 활성화

## <a name="prerequisites"></a>사전 요구 사항

Azure Stack Edge Pro device with GPU 디바이스를 구성하고 설정하기 전에 다음 사항을 확인합니다.

* 물리적 디바이스: 
    
    - [Azure Stack Edge Pro 설치](azure-stack-edge-gpu-deploy-install.md)에서 설명한 대로 물리적 디바이스를 설치했습니다.
    - [네트워크, 컴퓨팅 네트워크, 웹 프록시 구성](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)에 자세히 설명된 대로 네트워크 및 컴퓨팅 네트워크 설정을 구성했습니다.
    - **디바이스** 페이지를 통해 디바이스 이름 또는 DNS 도메인을 변경한 경우 디바이스에서 디바이스 인증서를 직접 업로드하거나 생성했습니다. 이 단계를 수행하지 않은 경우 디바이스 활성화 중에 오류가 표시되고 활성화가 차단됩니다. 자세한 내용을 보려면 [인증서 구성](azure-stack-edge-gpu-deploy-configure-certificates.md)으로 이동합니다.
    
* Azure Stack Edge Pro 디바이스를 관리하기 위해 만든 Azure Stack Edge 서비스의 활성화 키가 있습니다. 자세한 내용은 [Azure Stack Edge Pro 배포 준비](azure-stack-edge-gpu-deploy-prep.md)를 참조하세요.


## <a name="activate-the-device"></a>디바이스 활성화

1. 디바이스의 로컬 웹 UI에서 **시작** 페이지로 이동합니다.
2. **활성화** 타일에서 **활성화**를 선택합니다. 

    ![로컬 웹 UI "클라우드 세부 정보" 페이지](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. **활성화** 창에 [Azure Stack Edge Pro의 활성화 키 가져오기](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key)에서 얻은 **활성화 키**를 입력합니다.

4. **적용**을 선택합니다.

    ![로컬 웹 UI "클라우드 세부 정보" 페이지 2](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. 먼저 디바이스가 활성화됩니다. 이제 키 파일을 다운로드하라는 메시지가 표시됩니다.
    
    ![로컬 웹 UI "클라우드 세부 정보" 페이지 3](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
    **다운로드 및 계속**을 선택하고 디바이스 외부의 안전한 위치에 *device-serial-no.json* 파일을 저장합니다. **이 키 파일에는 OS 디스크의 복구 키와 디바이스의 데이터 디스크가 포함되어 있습니다**. 나중에 시스템을 복구할 때 이 키가 필요할 수 있습니다.

    *json* 파일의 내용은 다음과 같습니다.

        
    ```json
    {
      "Id": "<Device ID>",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "<BitLocker key for data disk>",
        "hcsdata": "<BitLocker key for data disk>"
      },
      "SystemVolumeBitLockerRecoveryKey": "<BitLocker key for system volume>",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```
        
 
    다음 표에서는 다양한 키에 대해 설명합니다.
    
    |필드  |Description  |
    |---------|---------|
    |`Id`    | 디바이스의 ID입니다.        |
    |`DataVolumeBitLockerExternalKeys`|데이터 디스크의 BitLockers 키이며 디바이스에서 로컬 데이터를 복구하는 데 사용됩니다.|
    |`SystemVolumeBitLockerRecoveryKey`| 시스템 볼륨의 BitLocker 키입니다. 이 키는 디바이스의 시스템 구성 및 시스템 데이터 복구에 도움이 됩니다. |
    |`ServiceEncryptionKey`| 이 키는 Azure 서비스를 통과하는 데이터를 보호합니다. 이 키는 Azure 서비스가 손상되더라도 저장된 정보가 손상되지 않도록 합니다. |

6. **개요** 페이지로 이동합니다. 디바이스 상태는 **활성화됨**으로 표시되어야 합니다.

    ![로컬 웹 UI "클라우드 세부 정보" 페이지 4](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
디바이스 활성화가 완료되었습니다. 이제 디바이스에서 공유를 추가할 수 있습니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아보았습니다.

> [!div class="checklist"]
> * 사전 요구 사항
> * 물리적 디바이스 활성화

Azure Stack Edge Pro 디바이스를 사용하여 데이터를 전송하는 방법은

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro를 사용하여 데이터 전송](./azure-stack-edge-j-series-deploy-add-shares.md)을 참조하세요.
