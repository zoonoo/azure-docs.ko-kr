---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/07/2021
ms.author: alkohli
ms.openlocfilehash: a46552639b9cdea135216e544ec4c51f4d9dda3d
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109736257"
---
Microsoft에서 디바이스를 수신하고 검사하면 주문 상태가 **수신됨** 으로 업데이트됩니다. 디바이스에 손상이나 변조 흔적이 있는지 물리적으로 확인됩니다.

확인이 완료되면 Data Box가 Azure 데이터 센터의 네트워크에 연결됩니다. 데이터 복사가 자동으로 시작됩니다. 데이터 크기에 따라 복사 작업을 완료하는 데 몇 시간에서 몇 일이 걸릴 수 있습니다. 포털에서 복사 작업 진행 상황을 모니터링할 수 있습니다.

### <a name="review-copy-errors-from-upload"></a>업로드에서 복사 오류 검토

다시 시도할 수 없는 오류로 인해 파일을 업로드하지 못한 경우 계속하기 전에 오류를 검토하라는 알림이 표시됩니다. 오류는 데이터 복사 로그에 나열됩니다.

이러한 오류는 수정할 수 없습니다. 업로드는 오류를 나타내며 완료되었습니다. 이 알림을 통해 네트워크 전송 또는 새 가져오기 주문을 통해 다른 업로드를 시도하기 전에 해결해야 하는 구성 문제에 대해 알 수 있습니다. 지침에 대해서는 [Azure Data Box 및 Azure Data Box Heavy 장치에서 업로드의 복사 오류 검토](../articles/databox/data-box-troubleshoot-data-upload.md)를 참조하세요.

오류를 검토하고 계속할 준비가 되었음을 확인하면 디바이스에서 데이터를 지워도 안전합니다. 해당 주문은 14일이 지나면 자동으로 완료됩니다. 알림에 대해 조치를 취하여 작업을 더 빠르게 진행할 수 있습니다.

[!INCLUDE [data-box-review-nonretryable-errors](data-box-review-nonretryable-errors.md)]


### <a name="verify-data-in-completed-upload"></a>완료된 업로드의 데이터 확인

데이터 업로드가 완료되면 주문 상태는 **완료됨** 으로 업데이트됩니다.

원본에서 데이터를 삭제하기 전에 데이터가 Azure에 업로드되었는지 확인합니다. 데이터는 다음 위치에 있을 수 있습니다.

- Azure Storage 계정. Data Box에 데이터를 복사하면 데이터가 Azure Storage 계정의 다음 경로 중 하나에 업로드됩니다.

  - 블록 Blob 및 페이지 Blob의 경우: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files의 경우: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    또는 Azure Portal에서 Azure storage 계정으로 이동하여 찾아볼 수도 있습니다.

- 관리 디스크 리소스 그룹. 관리 디스크를 만들 때 VHD는 페이지 Blob으로 업로드된 다음, 관리 디스크로 변환됩니다. 관리 디스크는 주문을 만들 때 지정된 리소스 그룹에 연결됩니다. 

    - Azure에서 관리 디스크에 성공적으로 복사되면 Azure Portal의 **주문 세부 정보** 로 이동하여 관리 디스크에 대해 지정된 리소스 그룹을 기록할 수 있습니다.

        ![관리 디스크 리소스 그룹 식별](media/data-box-verify-upload-return/order-details-managed-disk-resource-groups.png)

        기록한 리소스 그룹으로 이동하여 관리 디스크를 찾습니다.

        ![리소스 그룹에 연결된 관리 디스크](media/data-box-verify-upload-return/managed-disks-resource-group.png)

    - VHDX, 동적 또는 차이점 보관용 VHD를 복사한 경우 VHDX 또는 VHD가 준비 스토리지 계정에 페이지 Blob으로 업로드되지만 VHD를 관리 디스크로 변환하지 못합니다. 준비 **스토리지 계정 > Blob** 으로 차례로 이동하고 적절한 컨테이너(표준 SSD, 표준 HDD 또는 프리미엄 SSD)를 선택합니다. VHD는 준비 스토리지 계정에 페이지 Blob으로 업로드되고 요금이 부과됩니다.


## <a name="erasure-of-data-from-data-box"></a>Data Box에서 데이터 지우기
 
Azure에 대한 업로드가 완료되면 Data Box 디스크의 데이터가 [NIST SP800-88 개정 1 지침](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)에 따라 지워집니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * 필수 구성 요소
> * 배송 준비
> * Microsoft에 Data Box 배송
> * Azure에 대한 데이터 업로드 확인
> * Data Box에서 데이터 지우기

다음 문서로 이동하여 로컬 웹 UI를 통해 Data Box를 관리하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [로컬 웹 UI를 사용하여 Azure Data Box 관리](../articles/databox/data-box-local-web-ui-admin.md)