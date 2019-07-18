---
title: Azure Data Box Edge의 컴퓨팅 기능을 사용한 고급 배포를 위해 데이터를 필터링하고 분석하기 위한 자습서 | Microsoft Docs
description: Data Box Edge에 컴퓨팅 역할을 구성하고 이 역할을 사용하여 고급 배포 흐름을 위해 데이터를 변환한 후에 Azure로 보내는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b446a3ebf92f6240d3bc02a148fbb8296efec926
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65950700"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-for-advanced-deployment-flow"></a>자습서: 고급 배포 흐름을 위해 Azure Data Box Edge를 사용하여 데이터 변환

이 자습서에서는 Azure Data Box Edge 디바이스에서 고급 배포 흐름을 위해 컴퓨팅 역할을 구성하는 방법에 대해 설명합니다. 컴퓨팅 역할이 구성되면 Data Box Edge에서 데이터를 변환한 후에 Azure로 보낼 수 있습니다.

디바이스에서 단순 또는 고급 배포 흐름을 위해 컴퓨팅을 구성할 수 있습니다.

|                  | 단순 배포                                | 고급 배포                   |
|------------------|--------------------------------------------------|---------------------------------------|
| 대상 사용자     | IT 관리자                                | 개발자                            |
| Type             | Data Box Edge 서비스를 사용하여 모듈 배포      | IoT Hub 서비스를 사용하여 모듈 배포 |
| 배포된 모듈 | Single                                           | 연결된 모듈 또는 여러 모듈           |


이 절차를 완료하는 데 약 20-30분이 걸릴 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 컴퓨팅 구성
> * 공유 추가
> * 트리거 추가
> * 계산 모듈 추가
> * 데이터 변환 및 전송 확인

 
## <a name="prerequisites"></a>필수 조건

Data Box Edge 디바이스에서 컴퓨팅 역할을 설정하기 전에 다음 사항을 확인합니다.

- [Azure Data Box Edge 연결, 설정 및 활성화](data-box-edge-deploy-connect-setup-activate.md)에서 설명한 대로 Data Box Edge 디바이스를 활성화했습니다.


## <a name="configure-compute"></a>컴퓨팅 구성

Data Box Edge에 컴퓨팅을 구성하려면 IoT Hub 리소스를 만들어야 합니다.

1. Data Box Edge 리소스의 Azure Portal에서 **개요**로 이동합니다. 오른쪽에 있는 **컴퓨팅** 타일에서 **시작**을 선택합니다.

    ![컴퓨팅 시작](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. **Edge 컴퓨팅 구성** 타일에서 **컴퓨팅 구성**을 선택합니다.

    ![컴퓨팅 시작](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. **Edge 컴퓨팅 구성** 블레이드에서 다음 정보를 입력합니다.

   
    |필드  |값  |
    |---------|---------|
    |IoT Hub     | **새로 만들기** 또는 **기존 항목** 중에서 선택합니다. <br> 표준 계층(S1)을 사용하여 IoT 리소스를 만드는 것이 기본입니다. 무료 계층 IoT 리소스를 사용하려면 IoT 리소스를 새로 만든 후 기존 리소스를 선택합니다. <br> 어떤 방법을 선택하든, IoT Hub 리소스는 Data Box Edge 리소스에서 사용한 것과 동일한 구독 및 리소스 그룹을 사용합니다.     |
    |Name     |IoT Hub 리소스의 이름을 입력합니다.         |

    ![컴퓨팅 시작](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. **만들기**를 선택합니다. IoT Hub 리소스 만들기는 몇 분 정도 걸립니다. IoT Hub 리소스가 만들어지면 컴퓨팅 구성을 표시하도록 **Edge 컴퓨팅 구성** 타일이 업데이트됩니다. Edge 컴퓨팅 역할이 구성되었는지 확인하려면 **컴퓨팅 구성** 타일의 **구성 보기**를 선택합니다.
    
    ![컴퓨팅 시작](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    Edge 컴퓨팅 역할이 Edge 디바이스에 설정되면 두 가지 디바이스, 즉 IoT 디바이스와 IoT Edge 디바이스가 만들어집니다. 이 두 디바이스는 모두 IoT Hub 리소스에서 볼 수 있습니다. IoT Edge 런타임 역시 이 IoT Edge 디바이스에서 실행됩니다.

    이 시점에서 IoT Edge 디바이스에는 Linux 플랫폼만 사용할 수 있습니다.


## <a name="add-shares"></a>공유 추가

이 자습서의 고급 배포 작업을 수행하려면 Edge 공유와 Edge 로컬 공유가 필요합니다.

1. 다음 단계에 따라 디바이스에 Edge 공유를 추가합니다.

    1. Data Box Edge 리소스에서 **Edge 컴퓨팅 > 시작**으로 이동합니다.
    2. **공유 추가** 타일에서 **추가**를 선택합니다.
    3. **공유 추가** 블레이드에서 공유 이름을 입력하고 공유 유형을 선택합니다.
    4. Edge 공유를 탑재하려면 **Edge 컴퓨팅과 공유 사용** 확인란을 선택합니다.
    5. **스토리지 계정**, **스토리지 서비스**, 기존 사용자를 선택한 다음, **만들기**를 선택합니다.

        ![Edge 공유 추가](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    Edge 공유가 생성되면 성공적으로 생성되었다는 알림이 수신됩니다. 공유 목록이 새 공유를 반영하도록 업데이트됩니다.

2. 이전 단계의 모든 과정을 반복하고 **Edge 로컬 공유로 구성** 확인란을 선택하여 Edge 디바이스에 Edge 로컬 공유를 추가합니다. 로컬 공유의 데이터는 디바이스에 계속 남아 있습니다.

    ![Edge 로컬 공유 추가](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. **공유** 블레이드에 업데이트된 공유 목록이 표시됩니다.

    ![업데이트된 공유 목록](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. 새로 만든 로컬 공유의 속성을 보려면 목록에서 공유를 선택합니다. **Edge 컴퓨팅 모듈의 로컬 탑재 지점** 상자에서 이 공유에 해당하는 값을 복사합니다.

    모듈을 배포할 때 이 로컬 탑재 지점을 사용합니다.

    ![“Edge 컴퓨팅 모듈의 로컬 탑재 지점” 상자](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. 사용자가 만든 Edge 공유의 속성을 보려면 목록에서 공유를 선택합니다. **Edge 컴퓨팅 모듈의 로컬 탑재 지점** 상자에서 이 공유에 해당하는 값을 복사합니다.

    모듈을 배포할 때 이 로컬 탑재 지점을 사용합니다.

    ![사용자 지정 모듈 추가](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>트리거 추가

1. **Edge 컴퓨팅 > 트리거**로 이동합니다. **+ 트리거 추가**를 선택합니다.

    ![트리거 추가](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. **트리거 추가** 블레이드에서 다음 값을 입력합니다.

    |필드  |값  |
    |---------|---------|
    |트리거 이름     | 트리거의 고유한 이름입니다.         |
    |트리거 유형     | **파일** 트리거를 선택합니다. 입력 공유에 파일이 기록되는 것처럼 파일 이벤트가 발생할 때마다 파일 트리거가 실행됩니다. 반면에, 예약된 트리거는 사용자가 정의한 일정에 따라 실행됩니다. 이 예제에서는 파일 트리거가 필요합니다.    |
    |입력 공유     | 입력 공유를 선택합니다. 이 예에서는 Edge 로컬 공유가 입력 공유입니다. 여기에 사용된 모듈은 Edge 로컬 공유의 파일을 클라우드에 업로드되는 Edge 공유로 이동합니다.        |

    ![트리거 추가](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. 트리거가 만들어진 후 알림이 표시됩니다. 새로 만든 트리거를 표시하도록 트리거 목록이 업데이트됩니다. 방금 만든 트리거를 선택합니다.

    ![트리거 추가](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. 샘플 경로를 복사하고 저장합니다. 이 샘플 경로를 수정하고 나중에 IoT Hub에서 사용합니다.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![트리거 추가](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>모듈 추가

이 Edge 디바이스에는 사용자 지정 모듈이 없습니다. 사용자 지정 또는 미리 작성된 모듈을 추가할 수 있습니다. 사용자 지정 모듈을 만드는 방법을 알아보려면 [Data Box Edge 디바이스용 C# 모듈 개발](data-box-edge-create-iot-edge-module.md)로 이동합니다.

이 섹션에서는 사용자 지정 모듈을 [Data Box Edge용 C# 모듈 개발](data-box-edge-create-iot-edge-module.md)에서 만든 IoT Edge 디바이스에 추가합니다. 이 사용자 지정 모듈은 Edge 디바이스의 Edge 로컬 공유에서 파일을 가져와 디바이스의 Edge(클라우드) 공유로 이동합니다. 그런 다음, 클라우드 공유에서 파일을 클라우드 공유와 연결된 Azure 스토리지 계정에 푸시합니다.

1. **Edge 컴퓨팅 > 시작**으로 이동합니다. **모듈 추가** 타일에서 시나리오 유형을 **고급**으로 선택합니다. **IoT Hub로 이동**을 선택합니다.

    ![고급 배포 선택](./media/data-box-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. IoT Hub 리소스에서 **IoT Edge 디바이스**로 이동한 후 IoT Edge 디바이스를 선택합니다.

    ![IoT Hub에서 IoT Edge 디바이스로 이동](./media/data-box-edge-deploy-configure-compute-advanced/add-module-2.png)

3. **디바이스 세부 정보**에서 **모듈 설정**을 선택합니다.

    ![모듈 설정 링크](./media/data-box-edge-deploy-configure-compute-advanced/add-module-3.png)

4. **모듈 추가**에서 다음을 수행합니다.

    1. 사용자 지정 모듈의 컨테이너 레지스트리 설정에 대한 이름, 주소, 사용자 이름 및 암호를 입력합니다.
    이름, 주소 및 나열된 자격 증명은 일치하는 URL이 있는 모듈을 검색하는 데 사용됩니다. 이 모듈을 배포하려면 **배포 모듈** 아래에서 **IoT Edge 모듈**을 선택합니다. 이 IoT Edge 모듈은 Data Box Edge 디바이스와 연결된 IoT Edge 디바이스에 배포할 수 있는 Docker 컨테이너입니다.

        ![모듈 설정 페이지](./media/data-box-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. IoT Edge 사용자 지정 모듈에 대한 설정을 지정합니다. 다음 값을 입력합니다.
     
        |필드  |값  |
        |---------|---------|
        |Name     | 모듈의 고유한 이름입니다. 이 모듈은 Data Box Edge와 연결된 IoT Edge 디바이스에 배포할 수 있는 Docker 컨테이너입니다.        |
        |이미지 URI     | 모듈의 해당 컨테이너 이미지에 대한 이미지 URI입니다.        |
        |자격 증명 필요     | 이 옵션을 선택하면 사용자 이름과 암호를 사용하여 URL이 일치하는 모듈을 검색하게 됩니다.        |
    
        **컨테이너 만들기 옵션** 상자에 Edge 공유 및 Edge 로컬 공유에 대한 이전 단계에서 복사한 Edge 모듈의 로컬 탑재 지점을 입력합니다.

        > [!IMPORTANT]
        > 여기서 사용한 경로는 컨테이너에 탑재되므로 컨테이너에 필요한 기능과 일치해야 합니다. [사용자 지정 모듈 만들기](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code)를 수행하는 경우 해당 모듈에 지정한 코드에는 복사한 경로가 필요합니다. 이러한 경로는 수정하지 마세요.
    
        **컨테이너 만들기 옵션** 상자에서 다음 샘플을 붙여넣을 수 있습니다.
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        모듈에 사용한 환경 변수도 입력합니다. 환경 변수는 모듈이 실행될 환경을 정의하는 데 도움이 되는 선택적 정보를 제공합니다.

        ![컨테이너 만들기 옵션 상자](./media/data-box-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. 필요한 경우 고급 Edge 런타임 설정을 구성한 다음, **다음**을 클릭합니다.

        ![사용자 지정 모듈 추가](./media/data-box-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5.  **경로 지정**에서 모듈 간 경로를 설정합니다.  
    
    ![경로 지정](./media/data-box-edge-deploy-configure-compute-advanced/add-module-7.png)

    *경로*를 이전에 복사한 다음 경로 문자열로 바꿀 수 있습니다. 이 예제에서 데이터를 클라우드 공유에 푸시하는 로컬 공유의 이름을 입력합니다. `modulename`을 모듈의 이름으로 바꿉니다. **다음**을 선택합니다.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![경로 지정 섹션](./media/data-box-edge-deploy-configure-compute-advanced/add-module-8.png)

6.  **배포 검토**에서 모든 설정을 검토한 다음, **제출**을 선택하여 배포에 사용할 수 있도록 모듈을 제출합니다.

    ![모듈 설정 페이지](./media/data-box-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    이 작업은 모듈 배포를 시작합니다. 배포가 완료되면 모듈의 **런타임 상태**는 **실행 중**입니다.

    ![사용자 지정 모듈 추가](./media/data-box-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>데이터 변환 확인, 전송

마지막 단계는 모듈이 예상대로 연결되어 실행되는지 확인하는 것입니다. IoT Hub 리소스에서 IoT Edge 디바이스에 대한 모듈의 런타임 상태가 실행 중이어야 합니다.

다음 단계에 따라 Azure로의 데이터 변환 및 전송을 확인합니다.
 
1.  파일 탐색기에서, 이전에 만든 Edge 로컬 및 Edge 공유에 모두 연결합니다.

    ![데이터 변환 확인](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1.  로컬 공유에 데이터를 추가합니다.

    ![데이터 변환 확인](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    데이터가 클라우드 공유로 이동합니다.

    ![데이터 변환 확인](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    그런 다음, 데이터가 클라우드 공유에서 스토리지 계정으로 푸시됩니다. 데이터를 보려면 스토리지 계정으로 이동하고 **스토리지 탐색기**를 선택합니다. 스토리지 계정에 업로드된 데이터를 볼 수 있습니다.

    ![데이터 변환 확인](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
유효성 검사 프로세스를 완료했습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 컴퓨팅 구성
> * 공유 추가
> * 트리거 추가
> * 계산 모듈 추가
> * 데이터 변환 및 전송 확인

Data Box Edge 디바이스를 관리하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [로컬 웹 UI를 사용하여 Data Box Edge 관리](data-box-edge-manage-access-power-connectivity-mode.md)
