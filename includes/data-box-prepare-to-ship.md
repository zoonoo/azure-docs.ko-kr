---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/25/2019
ms.author: alkohli
ms.openlocfilehash: 1d52117440028c75b249f469f2b3576c2ab1c5c5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182867"
---
마지막 단계는 배송할 디바이스를 준비하는 것입니다. 이 단계에서는 모든 디바이스 공유가 오프라인 상태가 됩니다. 이 프로세스를 시작한 후에는 공유에 액세스할 수 없습니다.

> [!IMPORTANT]
> 배송 준비는 Azure 명명 규칙을 준수하지 않는 데이터를 플래그 지정하기 때문에 필요합니다. 이 단계를 건너뛰면 부적합한 데이터로 인해 잠재적인 데이터 업로드 오류가 발생할 수 있습니다.

1. **배송 준비**로 이동하여 **준비 시작**을 클릭합니다. 기본적으로 데이터가 복사되는 동안 체크섬이 계산됩니다. 배송 준비는 체크섬 계산을 완료하고 파일(  -BOM 파일) 목록을 만듭니다. 체크섬 컴퓨팅은 데이터의 크기에 따라 다소 시간이 걸릴 수 있습니다. 
   
    ![배송 준비 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    어떤 이유로든 디바이스 준비 과정을 중지하려면 **준비 중지**를 클릭합니다. 나중에 배송 준비를 다시 시작할 수 있습니다.
        
    ![배송 준비 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. 배송 준비가 시작되면 디바이스 공유가 오프라인으로 전환됩니다. 디바이스가 준비되면 배송 레이블을 다운로드하라는 알림이 표시됩니다.

    ![포장용 레이블 다운로드 알림](media/data-box-prepare-to-ship/download-shipping-label-reminder.png)

3. 디바이스 준비가 완료되면 디바이스 상태가 *배송 준비 완료*로 업데이트되고 디바이스가 잠깁니다.
        
    ![배송 준비 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    디바이스에 더 많은 데이터를 복사하려는 경우 디바이스 잠금을 해제하고, 더 많은 데이터를 복사한 후 배송 준비를 다시 실행합니다.

    이 단계에서 오류가 있는 경우 오류 로그를 다운로드하고 오류를 해결해야 합니다. 오류가 해결되면 **배송 준비**를 실행합니다.

4. 배송 준비가 성공적으로 완료(오류 없음)된 후 이 프로세스에서 복사한 파일(매니페스트라고도 함)의 목록을 다운로드합니다. 나중에 이 목록을 사용하여 Azure에 업로드된 파일을 확인할 수 있습니다. 자세한 내용은 [배송 준비 동안 BOM 파일 검사](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship)를 참조하세요.
        
    ![배송 준비 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

5. 디바이스를 종료합니다. **종료하거나 다시 시작** 페이지로 이동하여 **종료**를 클릭합니다. 확인을 요청하는 메시지가 표시되면 **확인**을 클릭하여 계속 진행합니다.

6. 케이블을 분리합니다. 다음 단계는 디바이스를 Microsoft로 보내는 것입니다.
