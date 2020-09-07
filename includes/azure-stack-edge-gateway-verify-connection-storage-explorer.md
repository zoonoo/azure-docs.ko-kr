---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/02/2019
ms.author: alkohli
ms.openlocfilehash: 3474a97f4b05fd1f944e18abebcef927232453e4
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89080573"
---
처음으로 Storage Explorer를 사용하는 경우 다음 단계를 수행해야 합니다.

1. 맨 위 명령 모음에서 **편집 > 대상 Azure Stack API**로 이동합니다.

    ![Storage Explorer 구성](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-1.png)

2. 변경 내용이 적용되도록 Storage Explorer를 다시 시작합니다.


다음 단계에 따라 스토리지 계정에 연결하고 연결을 확인합니다.

1. Storage Explorer에서 스토리지 계정을 선택합니다. 마우스 오른쪽 단추를 클릭하고 **Azure Storage에 연결** 옵션을 선택합니다. 

    ![Storage Explorer 구성](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-2.png)

2. **Azure Storage에 연결** 대화 상자에서 **스토리지 계정 이름 및 키 사용**을 선택합니다.

    ![Storage Explorer 구성](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-3.png)

2. **이름 및 키로 연결** 대화 상자에서 다음 단계를 수행합니다.

    1. Edge 스토리지 계정에 대한 표시 이름을 입력합니다. 
    2. Edge 스토리지 계정 이름을 제공합니다.
    3. Azure Resource Manager를 통해 디바이스 로컬 API에서 가져온 액세스 키를 붙여넣습니다.
    4. Storage 도메인을 **기타(아래 입력)** 로 선택한 다음, 다음 형식으로 Blob 서비스 엔드포인트의 접미사를 제공합니다. `<appliance name>.<DNSdomain>` 
    5. *http*를 통해 전송되는 **HTTP 사용** 옵션을 선택합니다. 
    6. **다음**을 선택합니다.

    ![Storage Explorer 구성](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-4.png)    

3. **연결 요약** 대화 상자에서 제공된 정보를 검토합니다. **연결**을 선택합니다.

    ![Storage Explorer 구성](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-5.png)

4. 성공적으로 추가한 계정은 Storage Explorer의 왼쪽 창에 해당 이름에 (외부, 기타)가 추가된 상태로 표시됩니다. **Blob 컨테이너**를 선택하여 컨테이너를 봅니다.

    ![Blob 컨테이너 보기](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-6.png)

다음 단계는 데이터 전송이 실제로 이 연결을 통해 제대로 작동하고 있는지 확인하는 것입니다.

다음 단계를 수행하여 디바이스의 Edge 스토리지 계정에 데이터를 로드하면 매핑된 Azure Storage 계정에 자동으로 계층화됩니다.

1. Edge 스토리지 계정에 데이터를 로드하려는 컨테이너를 선택합니다. **업로드**를 선택한 다음, **파일 업로드**를 선택합니다.

    ![데이터 전송 확인](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-1.png)

2. **파일 업로드** 대화 상자에서 업로드하려는 파일을 찾아서 선택합니다. **다음**을 선택합니다.

    ![데이터 전송 확인](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-2.png)

3. 파일이 업로드되었는지 확인합니다. 업로드된 파일이 컨테이너에 표시됩니다.

    ![데이터 전송 확인](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-3.png)

4. 다음에는 이 Edge 스토리지 계정에 매핑된 Azure Storage 계정에 연결합니다. Edge 스토리지 계정에 업로드되는 모든 데이터는 Azure Storage 계정으로 자동으로 계층화됩니다. 
    
    Azure Storage 계정에 대한 연결 문자열을 가져오려면 **Azure Storage 계정 > 액세스 키**로 이동하여 연결 문자열을 복사합니다.

    ![데이터 전송 확인](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-5.png)

    연결 문자열을 사용하여 Azure Storage 계정에 연결합니다.  

    ![데이터 전송 확인](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-4.png)


5. **연결 요약** 대화 상자에서 제공된 정보를 검토합니다. **연결**을 선택합니다.

    ![데이터 전송 확인](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-6.png)

6. Edge 스토리지 계정에서 업로드한 파일이 Azure Storage 계정으로 전송된 것을 볼 수 있습니다.

    ![데이터 전송 확인](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-7.png)