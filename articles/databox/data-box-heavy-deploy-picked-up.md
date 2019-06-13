---
title: Azure Data Box Heavy 반송 자습서 | Microsoft Docs
description: Azure Data Box Heavy를 Microsoft에 반송하는 방법 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: 31185900ede0fae74a04f98eaecee7379fb1c4fe
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427814"
---
# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure-preview"></a>자습서: Azure Data Box Heavy 반송 및 Azure에 대한 데이터 업로드 확인(미리 보기)


이 자습서에서는 Azure Data Box Heavy를 반송하고 Azure에 업로드된 데이터를 확인하는 방법을 설명합니다.

이 자습서에서는 다음과 같은 주제에 대해 학습합니다.

> [!div class="checklist"]
> * 필수 조건
> * 배송 준비
> * Microsoft에 Data Box Heavy 배송
> * Azure에 대한 데이터 업로드 확인
> * Data Box Heavy에서 데이터 지우기

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 사항을 확인합니다.

- [자습서: Azure Data Box에 데이터 복사 및 확인](data-box-heavy-deploy-copy-data.md)을 완료했는지 확인합니다.
- 복사 작업이 완료되었습니다. 복사 작업이 진행 중이면 배송 준비를 실행할 수 없습니다.

## <a name="prepare-to-ship"></a>배송 준비

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

## <a name="ship-data-box-heavy-back"></a>Data Box Heavy 반송

1. 디바이스의 전원이 꺼져 있고 모든 케이블이 분리되었는지 확인합니다. 디바이스 뒷면에서 액세스할 수 있는 트레이에 4개의 전원 코드를 스풀링하고 안전하게 고정합니다.
2. 디바이스를 미국 또는 유럽에서 발송하는 경우 배송 업체는 FedEx입니다.

    1. 배송 픽업을 예약하려면 배송 업체의 현지 번호로 전화합니다.
    2. [Data Box Operations](mailto:DataBoxOps@microsoft.com)에 연락하여 픽업에 관해 알려주고 반송용 배송 레이블을 받습니다.
    3. 배송 레이블은 디바이스 전면의 투명한 화면에 표시되어야 합니다.
    4. 이전 배송의 이전 배송 레이블을 디바이스에서 제거합니다.
3. 운송업체에서 Data Box Heavy를 픽업하고 나면 포털의 주문 상태가 **픽업됨**으로 업데이트됩니다. 추적 ID도 표시됩니다.

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

Microsoft에서 디바이스를 수신하고 검사하면 주문 상태가 **수신됨**으로 업데이트됩니다. 디바이스에 손상이나 변조 흔적이 있는지 물리적으로 확인됩니다.

확인이 완료되면 Data Box Heavy가 Azure 데이터 센터의 네트워크에 연결됩니다. 데이터 복사가 자동으로 시작됩니다. 데이터 크기에 따라 복사 작업을 완료하는 데 몇 시간에서 몇 일이 걸릴 수 있습니다. 포털에서 복사 작업 진행 상황을 모니터링할 수 있습니다.

복사가 완료되면 주문 상태는 **완료됨**으로 업데이트됩니다.

원본에서 데이터를 삭제하기 전에 데이터가 Azure에 업로드되었는지 확인합니다. 데이터는 다음 위치에 있을 수 있습니다.

- Azure Storage 계정. Data Box에 데이터를 복사할 때, 데이터 형식에 따라 Azure Storage 계정에서 다음 경로 중 하나에 업로드됩니다.

  - 블록 Blob 및 페이지 Blob의 경우: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files의 경우: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    또는 Azure Portal에서 Azure storage 계정으로 이동하여 찾아볼 수도 있습니다.

- 관리 디스크 리소스 그룹. 관리 디스크를 만들 때 VHD는 페이지 Blob으로 업로드된 다음, 관리 디스크로 변환됩니다. 관리 디스크는 주문을 만들 때 지정된 리소스 그룹에 연결됩니다. 

    - Azure에서 관리 디스크에 성공적으로 복사되면 Azure Portal의 **주문 세부 정보**로 이동하여 관리 디스크에 대해 지정된 리소스 그룹을 기록할 수 있습니다.

        ![관리 디스크 리소스 그룹 식별](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        기록한 리소스 그룹으로 이동하여 관리 디스크를 찾습니다.

        ![리소스 그룹에 연결된 관리 디스크](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - VHDX 또는 동적/차이점 보관용 VHD를 복사한 경우 VHD/VHD가 준비 스토리지 계정에 페이지 Blob으로 업로드되지만 VHD를 관리 디스크로 변환하지 못합니다. 준비 **스토리지 계정 > Blob**으로 차례로 이동한 다음, 적절한 컨테이너(표준 SSD, 표준 HDD 또는 표준 HDD)를 선택합니다. VHD는 준비 스토리지 계정에 페이지 Blob으로 업로드됩니다.

## <a name="erasure-of-data-from-data-box-heavy"></a>Data Box Heavy에서 데이터 지우기
 
Azure에 대한 업로드가 완료되면 Data Box 디스크의 데이터가 [NIST SP800-88 개정 1 지침](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)에 따라 지워집니다. 지우기가 완료된 후에 [주문 기록을 다운로드](data-box-portal-admin.md#download-order-history)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * 필수 조건
> * 배송 준비
> * Microsoft에 Data Box Heavy 배송
> * Azure에 대한 데이터 업로드 확인
> * Data Box Heavy에서 데이터 지우기

다음 문서로 이동하여 로컬 웹 UI를 통해 Data Box Heavy를 관리하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [로컬 웹 UI를 사용하여 Azure Data Box 관리](./data-box-local-web-ui-admin.md)


