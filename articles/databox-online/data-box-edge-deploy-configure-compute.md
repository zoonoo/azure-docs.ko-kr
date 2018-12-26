---
title: Azure Data Box Edge를 사용하여 데이터 변환 | Microsoft Docs
description: Data Box Edge에 계산 역할을 구성하고 이 역할을 사용하여 데이터를 변환한 후에 Azure로 보내는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 77a2b8d2b5d3ac42dcbbe2db2b05d38657290073
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443790"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>자습서: Azure Data Box Edge를 사용하여 데이터 변환(미리 보기)

이 자습서에서는 Data Box Edge에 계산 역할을 구성하는 방법에 대해 설명합니다. 계산 역할이 구성되면 Data Box Edge에서 데이터를 변환한 후에 Azure로 보낼 수 있습니다.

이 절차를 완료하는 데 약 30-45분이 걸릴 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * IoT Hub 리소스 만들기
> * 계산 역할 설정
> * 계산 모듈 추가
> * 데이터 변환 및 전송 확인

> [!IMPORTANT]
> Data Box Edge는 미리 보기로 있습니다. 이 솔루션을 주문하고 배포하기 전에 [미리 보기에 대한 Azure 서비스 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 검토하세요.
 
## <a name="prerequisites"></a>필수 조건

Data Box Edge에 계산을 설정하기 전에 다음 사항을 확인합니다.

* Data Box Edge 디바이스가 [Azure Data Box Edge 연결 및 활성화](data-box-edge-deploy-connect-setup-activate.md)에서 자세히 설명한 대로 활성화됩니다.


## <a name="create-an-iot-hub-resource"></a>IoT Hub 리소스 만들기

Data Box Edge에 계산 역할을 설정하기 전에 먼저 IoT Hub 리소스를 만들어야 합니다.

자세한 지침은 [IoT Hub 만들기](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub)로 이동하세요. Data Box Edge 리소스에 사용한 것과 동일한 구독 및 리소스 그룹을 사용합니다.

![IoT Hub 리소스 만들기](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

Edge 계산 역할이 설정되지 않은 경우 다음 사항을 참고하세요.

- IoT Hub 리소스에는 IoT 디바이스 또는 IoT Edge 디바이스가 없습니다.
- Edge 로컬 공유는 만들 수 없습니다. 공유가 추가되면 Edge 계산에 대한 로컬 공유를 만드는 옵션이 활성화되지 않습니다.


## <a name="set-up-compute-role"></a>계산 역할 설정

Edge 계산 역할이 Edge 디바이스에 설정되면 두 가지 디바이스, 즉 IoT 디바이스와 IoT Edge 디바이스가 만들어집니다. 이 두 디바이스는 IoT Hub 리소스에서 볼 수 있습니다.

디바이스에서 계산 역할을 설정하려면 다음 단계를 수행합니다.

1. Data Box Edge 리소스로 이동한 다음, **개요**로 이동하여 **계산 역할 설정**을 클릭합니다. 

    ![계산 역할 설정](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    또한 **모듈**로 이동하여 **계산 구성**을 클릭할 수도 있습니다.

    ![계산 역할 설정](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
2. 드롭다운 목록에서 이전 단계에서 만든 **IoT Hub 리소스**를 선택합니다. 이 시점에서 IoT Edge 디바이스에는 Linux 플랫폼만 사용할 수 있습니다. **만들기**를 클릭합니다.

    ![계산 역할 설정](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
3. 계산 역할은 만드는 데 몇 분이 걸립니다. 이 릴리스의 버그로 인해 계산 역할이 만들어지더라도 화면이 새로 고쳐지지 않습니다. **모듈**로 이동하면 Edge 계산이 구성되어 있는지 확인할 수 있습니다.  

    ![계산 역할 설정](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

4. **개요**로 다시 이동하면 이제 화면이 업데이트되어 계산 역할이 구성되었음을 나타냅니다.

    ![계산 역할 설정](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
5. Edge 계산 역할을 만들 때 사용한 IoT Hub로 이동합니다. **IoT 장치**로 이동합니다. 이제 IoT 디바이스를 사용하도록 설정되었음을 확인할 수 있습니다. 

    ![계산 역할 설정](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

6. **IoT Edge**로 이동하면 IoT Edge 장치도 사용하도록 설정되었음을 확인할 수 있습니다.

    ![계산 역할 설정](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
7. IoT Edge 디바이스를 선택하고 클릭합니다. Edge 에이전트가 이 IoT Edge 디바이스에서 실행되고 있습니다. 

    ![계산 역할 설정](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

그러나 이 Edge 디바이스에는 사용자 지정 모듈이 없습니다. 이제 이 디바이스에 사용자 지정 모듈을 추가할 수 있습니다. 사용자 지정 모듈 만드는 방법을 알아보려면 [Data Box Edge용 C# 모듈 개발](data-box-edge-create-iot-edge-module.md)로 이동하세요.


## <a name="add-a-custom-module"></a>사용자 지정 모듈 추가

이 섹션에서는 사용자 지정 모듈을 [Data Box Edge용 C# 모듈 개발](data-box-edge-create-iot-edge-module.md)에서 만든 IoT Edge 디바이스에 추가합니다. 

이 절차에서는 사용된 사용자 지정 모듈이 Edge 디바이스의 로컬 공유에서 파일을 가져와 디바이스의 클라우드 공유로 이동하는 예를 사용합니다. 그런 다음, 클라우드 공유에서 파일을 클라우드 공유와 연결된 Azure 저장소 계정에 푸시합니다. 

1. 첫 번째 단계는 Edge 디바이스에 로컬 공유를 추가하는 것입니다. Data Box Edge 리소스에서 **공유**로 이동합니다. **+ 공유 추가**를 클릭합니다. 공유 이름을 제공하고 공유 유형을 선택합니다. 로컬 공유를 만들려면 **Edge 로컬 공유로 구성** 옵션을 선택합니다. **기존 사용자** 또는 **새로 만들기**를 선택합니다. **만들기**를 클릭합니다.

    ![사용자 지정 모듈 추가](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    로컬 NFS 공유를 만든 경우 다음 rsync 명령 옵션을 사용하여 파일을 공유에 복사합니다.

    `rsync --inplace <source file path> < destination file path>`

     rsync 명령에 대한 자세한 내용은 [Rsync 설명서](https://www.computerhope.com/unix/rsync.htm)로 이동하세요. 

 
2. 로컬 공유가 만들어지고 성공적인 만들기 알림을 받으면(이전에 공유 목록이 업데이트될 수 있지만 공유 만들기가 완료될 때까지 기다려야 함) 공유 목록으로 이동합니다. 

    ![사용자 지정 모듈 추가](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
3. 새로 만든 로컬 공유를 선택하고 클릭하여 공유에 대한 속성을 봅니다. 이 공유에 해당하는 **Edge 모듈에 대한 로컬 탑재 지점**을 복사하고 저장합니다.

    ![사용자 지정 모듈 추가](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
    Data Box Edge 디바이스에 만들어진 기존 클라우드 공유로 이동합니다. 다시 한 번, 이 클라우드 공유의 Edge 계산 모듈에 대한 로컬 탑재 지점을 복사하고 저장합니다. 이러한 로컬 탑재 지점은 모듈을 배포할 때 사용됩니다.

    ![사용자 지정 모듈 추가](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

4. 사용자 지정 모듈을 IoT Edge 디바이스에 추가하려면 IoT Hub 리소스, **IoT Edge 디바이스**로 차례로 이동합니다. 디바이스를 선택하고 클릭합니다. **장치 세부 정보** 위쪽의 명령 모음에서 **모듈 설정**을 클릭합니다. 

    ![사용자 지정 모듈 추가](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

5. **모듈 추가** 아래에서 다음 단계를 수행합니다.

    1. 사용자 지정 모듈의 **컨테이너 레지스트리 설정**에 대한 **이름**, **주소**, **사용자 이름** 및 **암호**를 입력합니다. 이름, 주소 및 나열된 자격 증명은 일치하는 URL이 있는 모듈을 검색하는 데 사용됩니다. 이 모듈을 배포하려면 **배포 모듈** 아래에서 **IoT Edge 모듈**을 선택합니다. 이 IoT Edge 모듈은 Data Box Edge 디바이스와 연결된 IoT Edge 디바이스에 배포할 수 있는 Docker 컨테이너입니다.

        ![사용자 지정 모듈 추가](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    2. IoT Edge 사용자 지정 모듈에 대한 설정을 지정합니다. 모듈의 **이름**과 해당 컨테이너 이미지에 대한 **이미지 URI**를 제공합니다. 
    
        ![사용자 지정 모듈 추가](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    3. **컨테이너 만들기 옵션**에서 클라우드 공유 및 로컬 공유에 대해 이전 단계에서 복사한 Edge 모듈에 대한 로컬 탑재 지점을 제공합니다(새 경로를 만드는 대신 이러한 경로를 사용해야 함). 로컬 탑재 지점이 [사용자 지정 코드로 모듈을 업데이트](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code)할 때 모듈에서 지정한 해당 **InputFolderPath** 및 **OutputFolderPath**로 매핑됩니다. 
    
        **컨테이너 만들기 옵션**에서 아래 표시된 샘플을 복사하여 붙여넣을 수 있습니다. 
        
        ```
        {
         "HostConfig": {
          "Binds": [
           "/home/hcsshares/mysmblocalshare:/home/LocalShare",
           "/home/hcsshares/mysmbshare1:/home/CloudShare"
           ]
         }
        }
        ```

        또한 모듈에 환경 변수도 여기에 제공합니다.

        ![사용자 지정 모듈 추가](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    4. 필요한 경우 **고급 Edge 런타임 설정 구성**을 선택하고, **다음**을 클릭합니다.

        ![사용자 지정 모듈 추가](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
6.  **경로 지정** 아래에서 모듈 간 경로를 설정합니다. 이 경우 데이터를 클라우드 공유에 푸시하는 로컬 공유의 이름을 제공합니다. **다음**을 클릭합니다.

    경로를 `"route": "FROM /* WHERE topic = 'mysmblocalshare' INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")"` 경로 문자열로 바꿉니다.

    ![사용자 지정 모듈 추가](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 
 
7.  **배포 검토** 아래에서 모든 설정을 검토하고, 충족되는 경우 배포에 사용할 수 있도록 모듈을 **제출**합니다.

    ![사용자 지정 모듈 추가](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
그러면 **모듈** 아래의 **IoT Edge 사용자 지정 모듈**에서와 같이 모듈 배포가 시작됩니다.

![사용자 지정 모듈 추가](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>데이터 변환 및 전송 확인

마지막 단계는 모듈이 예상대로 연결되어 실행되는지 확인하는 것입니다. 모듈이 실행되는지 확인하려면 다음 단계를 수행합니다.

1. IoT Hub 리소스에서 IoT Edge 디바이스에 대한 모듈의 런타임 상태가 실행 중이어야 합니다.

    ![데이터 변환 확인](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
2. 모듈을 선택하여 클릭하고 **모듈 ID 쌍**을 살펴봅니다. Edge 디바이스 및 모듈에 한 클라이언트 상태는 **연결됨**으로 표시되어야 합니다.

    ![데이터 변환 확인](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
3.  모듈이 실행되면 Data Box Edge 리소스의 Edge 모듈 목록에도 표시됩니다. 추가한 모듈의 **런타임 상태**는 **실행 중**입니다.

    ![데이터 변환 확인](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
4.  [파일 탐색기]를 통해 만든 로컬 및 클라우드 공유에 모두 연결합니다.

    ![데이터 변환 확인](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
5.  로컬 공유에 데이터를 추가합니다.

    ![데이터 변환 확인](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
6.  데이터가 클라우드 공유로 이동합니다.

    ![데이터 변환 확인](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

7.  그런 다음, 데이터가 클라우드 공유에서 저장소 계정으로 푸시됩니다. Storage 탐색기로 이동하여 데이터를 봅니다.

    ![데이터 변환 확인](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
이제 확인 프로세스가 완료되었습니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 Data Box Edge 항목에 대해 알아보았습니다.

> [!div class="checklist"]
> * IoT Hub 리소스 만들기
> * 계산 역할 설정
> * 계산 모듈 추가
> * 데이터 변환 및 전송 확인

Data Box Edge를 관리하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [로컬 웹 UI를 사용하여 Data Box Edge 관리](https://aka.ms/dbg-docs)


