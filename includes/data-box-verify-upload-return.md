---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/30/2019
ms.author: alkohli
ms.openlocfilehash: ca7b83d24f2416b224963559361faf5a7775cd0d
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631542"
---
Microsoft에서 디바이스를 수신하고 검사하면 주문 상태가 **수신됨**으로 업데이트됩니다. 디바이스에 손상이나 변조 흔적이 있는지 물리적으로 확인됩니다.

확인이 완료되면 Data Box가 Azure 데이터 센터의 네트워크에 연결됩니다. 데이터 복사가 자동으로 시작됩니다. 데이터 크기에 따라 복사 작업을 완료하는 데 몇 시간에서 몇 일이 걸릴 수 있습니다. 포털에서 복사 작업 진행 상황을 모니터링할 수 있습니다.

복사가 완료되면 주문 상태는 **완료됨**으로 업데이트됩니다.

원본에서 데이터를 삭제하기 전에 데이터가 Azure에 업로드되었는지 확인합니다. 데이터는 다음 위치에 있을 수 있습니다.

- Azure Storage 계정. Data Box에 데이터를 복사하면 데이터가 Azure Storage 계정의 다음 경로 중 하나에 업로드됩니다.

  - 블록 Blob 및 페이지 Blob의 경우: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files의 경우: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    또는 Azure Portal에서 Azure storage 계정으로 이동하여 찾아볼 수도 있습니다.

- 관리 디스크 리소스 그룹. 관리 디스크를 만들 때 VHD는 페이지 Blob으로 업로드된 다음, 관리 디스크로 변환됩니다. 관리 디스크는 주문을 만들 때 지정된 리소스 그룹에 연결됩니다. 

    - Azure에서 관리 디스크에 성공적으로 복사되면 Azure Portal의 **주문 세부 정보**로 이동하여 관리 디스크에 대해 지정된 리소스 그룹을 기록할 수 있습니다.

        ![관리 디스크 리소스 그룹 식별](media/data-box-verify-upload-return/order-details-managed-disk-resource-groups.png)

        기록한 리소스 그룹으로 이동하여 관리 디스크를 찾습니다.

        ![리소스 그룹에 연결된 관리 디스크](media/data-box-verify-upload-return/managed-disks-resource-group.png)

    - VHDX, 동적 또는 차이점 보관용 VHD를 복사한 경우 VHDX 또는 VHD가 준비 스토리지 계정에 페이지 Blob으로 업로드되지만 VHD를 관리 디스크로 변환하지 못합니다. 준비 **스토리지 계정 > Blob**으로 차례로 이동하고 적절한 컨테이너(표준 SSD, 표준 HDD 또는 프리미엄 SSD)를 선택합니다. VHD는 준비 스토리지 계정에 페이지 Blob으로 업로드되고 요금이 부과됩니다.


## <a name="erasure-of-data-from-data-box"></a>Data Box에서 데이터 지우기
 
Azure에 대한 업로드가 완료되면 Data Box 디스크의 데이터가 [NIST SP800-88 개정 1 지침](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)에 따라 지워집니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * 사전 요구 사항
> * 배송 준비
> * Microsoft에 Data Box 배송
> * Azure에 대한 데이터 업로드 확인
> * Data Box에서 데이터 지우기

다음 문서로 이동하여 로컬 웹 UI를 통해 Data Box를 관리하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [로컬 웹 UI를 사용하여 Azure Data Box 관리](../articles/databox/data-box-local-web-ui-admin.md)