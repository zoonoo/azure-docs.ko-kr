---
title: IoT Edge 모듈을 사용하여 GPU가 있는 Azure Stack Edge Pro에 컴퓨팅 워크로드 배포 | Microsoft Docs
description: Azure Stack Edge Pro GPU 디바이스에서 미리 만든 IoT Edge 모듈을 사용하여 컴퓨팅 워크로드를 실행하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: d10e27c80a9253de7482644debd19debce8f4e50
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055300"
---
# <a name="tutorial-run-a-compute-workload-with-iot-edge-module-on-azure-stack-edge-pro-gpu"></a>자습서: Azure Stack Edge Pro GPU에서 IoT Edge 모듈을 사용하여 컴퓨팅 워크로드 실행

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

이 자습서에서는 Azure Stack Edge Pro GPU 디바이스에서 IoT Edge 모듈을 사용하여 컴퓨팅 워크로드를 실행하는 방법을 알아봅니다. 컴퓨팅이 구성되면 디바이스에서 데이터를 변환한 후에 Azure로 보내게 됩니다.

이 절차를 완료하는 데 약 10-15분이 걸릴 수 있습니다.


이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 컴퓨팅 구성
> * 공유 추가
> * 컴퓨팅 모듈 추가
> * 데이터 변환 및 전송 확인

 
## <a name="prerequisites"></a>사전 요구 사항

Azure Stack Edge Pro GPU 디바이스에서 컴퓨팅 역할을 설정하기 전에 다음 사항을 확인합니다.

- [Azure Stack Edge Pro 활성화](azure-stack-edge-gpu-deploy-activate.md)에 설명된 대로 Azure Stack Edge Pro 디바이스를 활성화했습니다.
- 데이터에서 실행할 수 있는 IoT Edge 모듈이 있습니다. 이 자습서에서는 데이터를 디바이스의 Edge 로컬 공유에서 Azure Storage 계정으로 이동하는 위치의 Edge 공유로 데이터를 이동하는 `filemove2` 모듈을 사용했습니다.


## <a name="configure-compute"></a>컴퓨팅 구성

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]


## <a name="add-shares"></a>공유 추가

이 자습서의 간단한 배포 작업을 수행하려면 Edge 공유와 Edge 로컬 공유가 필요합니다.

1. 디바이스에서 Edge 공유를 추가하려면 다음 단계를 수행합니다.

    1. Azure Stack Edge 리소스에서 **클라우드 스토리지 게이트웨이 > 공유** 로 이동합니다.
    2. 명령 모음에서 **+ 공유 추가** 를 선택합니다.
    3. **공유 추가** 블레이드에서 공유 이름을 입력하고 공유 유형을 선택합니다.
    4. Edge 공유를 탑재하려면 **Edge 컴퓨팅과 공유 사용** 확인란을 선택합니다.
    5. **스토리지 계정**, **스토리지 서비스**, 기존 사용자를 선택한 다음, **만들기** 를 선택합니다.

        ![Edge 공유 추가](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-1.png) 


    > [!NOTE]
    > NFS 공유를 컴퓨팅에 탑재하려면 컴퓨팅 네트워크를 NFS Virtual IP 주소와 동일한 서브넷에 구성해야 합니다. 컴퓨팅 네트워크를 구성하는 방법에 대한 자세한 내용은 [Azure Stack Edge Pro에서 컴퓨팅 네트워크 사용](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)을 참조하세요.

    Edge 공유가 생성되면 성공적으로 생성되었다는 알림이 수신됩니다. 공유 목록이 업데이트될 수 있지만, 공유 만들기가 완료될 때까지 기다려야 합니다.

2. 디바이스에서 Edge 로컬 공유를 추가하려면 이전 단계의 모든 단계를 반복하고 **Edge 로컬 공유로 구성** 확인란을 선택합니다. 로컬 공유의 데이터는 디바이스에 계속 남아 있습니다.

    ![Edge 로컬 공유 추가](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-2.png)

    로컬 NFS 공유를 만든 경우 다음 원격 공유(rsync) 명령 옵션을 사용하여 파일을 공유에 복사합니다.

    `rsync <source file path> < destination file path>`

    `rsync` 명령에 대한 자세한 내용은 [`Rsync` 설명서](https://www.computerhope.com/unix/rsync.htm)를 참조하세요.
 
3. **클라우드 스토리지 게이트웨이 > 공유** 로 이동하여 업데이트된 공유 목록을 확인합니다.

    ![업데이트된 공유 목록](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>모듈 추가

사용자 지정 또는 미리 작성된 모듈을 추가할 수 있습니다. 디바이스는 미리 작성되거나 사용자 지정 모듈을 제공하지 않습니다. 사용자 지정 모듈을 만드는 방법을 알아보려면 [Azure Stack Edge Pro 디바이스용 C# 모듈 개발](./azure-stack-edge-gpu-create-iot-edge-module.md)로 이동합니다.

이 섹션에서는 사용자 지정 모듈을 [Azure Stack Edge Pro용 C# 모듈 개발](./azure-stack-edge-gpu-create-iot-edge-module.md)에서 만든 IoT Edge 디바이스에 추가합니다. 이 사용자 지정 모듈은 Edge 디바이스의 Edge 로컬 공유에서 파일을 가져와 디바이스의 Edge(클라우드) 공유로 이동합니다. 그런 다음, 클라우드 공유에서 파일을 클라우드 공유와 연결된 Azure 스토리지 계정에 푸시합니다.

모듈을 추가하려면 다음 단계를 수행합니다.

1. **IoT Edge > 모듈** 로 이동합니다. 명령 모음에서 **+ 모듈 추가** 를 선택합니다. 

2. **모듈 추가** 블레이드에서 다음 값을 입력합니다.

    
    |필드  |값  |
    |---------|---------|
    |Name     | 모듈의 고유한 이름입니다. 이 모듈은 Azure Stack Edge Pro와 연결된 IoT Edge 디바이스에 배포할 수 있는 Docker 컨테이너입니다.        |
    |이미지 URI     | 모듈의 해당 컨테이너 이미지에 대한 이미지 URI입니다.        |
    |자격 증명 필요     | 이 옵션을 선택하면 사용자 이름과 암호를 사용하여 URL이 일치하는 모듈을 검색하게 됩니다.        |
    |입력 공유     | 입력 공유를 선택합니다. 이 예에서는 Edge 로컬 공유가 입력 공유입니다. 여기에 사용된 모듈은 Edge 로컬 공유의 파일을 클라우드에 업로드되는 Edge 공유로 이동합니다.        |
    |출력 공유     | 출력 공유를 선택합니다. 이 예에서는 Edge 공유가 출력 공유입니다.        |
    |트리거 유형     | **파일** 또는 **예약** 중에서 선택합니다. 입력 공유에 파일이 기록되는 것처럼 파일 이벤트가 발생할 때마다 파일 트리거가 실행됩니다. 예약된 트리거는 사용자가 정의한 일정에 따라 실행됩니다.         |
    |트리거 이름     | 트리거의 고유한 이름입니다.         |
    |환경 변수| 모듈이 실행될 환경을 정의하는 데 도움이 되는 선택적 정보입니다.   |

    ![모듈 추가 및 구성](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-1.png)

3. **추가** 를 선택합니다. 모듈이 추가됩니다. **IoT Edge > 모듈** 페이지가 업데이트되어 모듈이 배포되었음이 표시됩니다. 추가한 모듈의 런타임 상태가 *실행 중* 일 것입니다.

    ![배포된 모듈](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>데이터 변환 및 전송 확인

마지막 단계는 예상대로 모듈이 실행되고 데이터를 처리하는지 확인하는 것입니다. IoT Hub 리소스에서 IoT Edge 디바이스에 대한 모듈의 런타임 상태가 실행 중이어야 합니다.

예상대로 모듈이 실행되고 데이터를 처리하는지 확인하려면 다음을 수행합니다.


1. 파일 탐색기에서, 이전에 만든 Edge 로컬 및 Edge 공유에 모두 연결합니다. 단계 참조 

    ![Edge 로컬 및 Edge 클라우드 공유에 연결](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-1.png) 
 
1. 로컬 공유에 데이터를 추가합니다.

    ![Edge 로컬 공유에 복사되는 파일](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-2.png) 
 
   데이터가 클라우드 공유로 이동합니다.

    ![Edge 클라우드 공유로 이동한 파일](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-3.png)  

   그런 다음, 데이터가 클라우드 공유에서 스토리지 계정으로 푸시됩니다. 데이터를 보려면 Storage Explorer 또는 포털의 Azure Storage를 사용할 수 있습니다.

    ![스토리지 계정의 데이터 확인](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-4.png)
 
유효성 검사 프로세스를 완료했습니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 컴퓨팅 구성
> * 공유 추가
> * 컴퓨팅 모듈 추가
> * 데이터 변환 및 전송 확인

Azure Stack Edge Pro 디바이스를 관리하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [로컬 웹 UI를 사용하여 Azure Stack Edge Pro 관리](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md)