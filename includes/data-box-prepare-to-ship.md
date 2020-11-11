---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 07/01/2020
ms.author: alkohli
ms.openlocfilehash: a78bce9f6e714aa5564791f6bb63996554beabf1
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375904"
---
마지막 단계는 배송할 디바이스를 준비하는 것입니다. 이 단계에서는 모든 디바이스 공유가 오프라인 상태가 됩니다. 이 프로세스를 시작한 후에는 공유에 액세스할 수 없습니다.

> [!IMPORTANT]
> 배송 준비는 Azure 명명 규칙을 준수하지 않는 데이터를 플래그 지정하기 때문에 필요합니다. 이 단계는 부적합한 데이터로 인한 잠재적인 데이터 업로드 실패를 방지합니다.

1. **배송 준비** 로 이동하여 **준비 시작** 을 클릭합니다. 기본적으로 데이터가 복사되는 동안 체크섬이 계산됩니다. 배송 준비는 체크섬 계산을 완료하고 파일(-BOM 파일) 목록을 만듭니다. 체크섬 컴퓨팅은 데이터의 크기에 따라 다소 시간이 걸릴 수 있습니다. 
   
    ![배송 준비 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    어떤 이유로든 디바이스 준비 과정을 중지하려면 **준비 중지** 를 클릭합니다. 나중에 배송 준비를 다시 시작할 수 있습니다.
        
    ![배송 준비 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. 배송 준비가 시작되면 디바이스 공유가 오프라인으로 전환됩니다. <!--You see a reminder to download the shipping label once the device is ready.--> 디바이스 준비가 완료되면 디바이스 상태가 *배송 준비 완료* 로 업데이트되고 디바이스가 잠깁니다.
        
    ![배송 준비 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    디바이스에 더 많은 데이터를 복사하려는 경우 디바이스 잠금을 해제하고, 더 많은 데이터를 복사한 후 배송 준비를 다시 실행합니다.

    이 단계에서 오류가 발생하는 경우 상태가 *검사가 완료되었지만 오류 발생* 으로 업데이트됩니다. 디바이스를 잠금 해제하고 **연결 및 복사** 페이지로 이동하여 문제 목록을 다운로드하고 오류를 해결합니다.

    ![배송 준비 4](media/data-box-prepare-to-ship/prepare-to-ship4.png)

    오류가 해결되면 **배송 준비** 를 실행합니다.

4. 배송 준비가 성공적으로 완료되면(오류 없음) 다음 단계를 수행합니다.

    1. 완료 참조 번호를 기록해 둡니다. 사용자가 있는 국가에 따라 이 번호는 다른 작업에 필요할 수 있습니다.
    2. 이 프로세스에서 복사된 파일 목록(매니페스트라고도 함)을 다운로드합니다. 나중에 이 목록을 사용하여 Azure에 업로드된 파일을 확인할 수 있습니다. 자세한 내용은 [배송 준비 동안 BOM 파일 검사](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship)를 참조하세요.
        
        ![배송 준비 5](media/data-box-prepare-to-ship/prepare-to-ship5.png)
    3. 디바이스에 대한 배송 지침을 선택하고 다운로드합니다. 배송 지침은 사용자가 있는 국가에 따라 다릅니다.
    4. E-ink에 배송 레이블이 표시되지 않는 경우 여기에서 역 배송 레이블을 다운로드할 수 있습니다. 

5. 디바이스를 종료합니다. **종료하거나 다시 시작** 페이지로 이동하여 **종료** 를 클릭합니다. 확인을 요청하는 메시지가 표시되면 **확인** 을 클릭하여 계속 진행합니다.

6. 케이블을 분리합니다. 다음 단계는 디바이스를 Microsoft로 보내는 것입니다.
