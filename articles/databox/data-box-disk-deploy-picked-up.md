---
title: Microsoft Azure Data Box Disk 반송 | Microsoft Docs
description: 이 자습서를 사용하여 Microsoft에 Azure Data Box Disk를 배송하는 방법 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 0dd0474ad1ad360fd82cfdf746d2e9837f74833a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108378"
---
# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>자습서: Azure Data Box Disk 반환 및 Azure에 대한 데이터 업로드 확인

이 자습서는 Azure Data Box Disk 배포 시리즈의 마지막 자습서입니다. 이 자습서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Microsoft에 Data Box Disk 배송
> * Azure에 대한 데이터 업로드 확인
> * Data Box Disk에서 데이터 지우기

## <a name="prerequisites"></a>필수 조건

시작하기 전에 [자습서: Azure Data Box Disk에 데이터 복사 및 확인](data-box-disk-deploy-copy-data.md)을 참조하세요.

## <a name="ship-data-box-disk-back"></a>Data Box Disk 반송

1. 데이터 유효성 검사가 완료되면 디스크를 분리합니다. 연결 케이블을 제거합니다.
2. 완충재를 사용하여 모든 디스크 및 연결 케이블을 래핑하고 배송 상자에 배치합니다.
3. 상자에 부착된 투명 플라스틱 케이스에서 반송 포장용 레이블을 사용합니다. 레이블이 손상되거나 분실된 경우 Azure Portal에서 새로운 포장용 레이블을 다운로드하고 디바이스에 부착합니다. **개요 > 포장용 레이블 다운로드**로 이동합니다.

    ![포장용 레이블 다운로드](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    이 작업은 아래 표시된 것처럼 반송 포장용 레이블을 다운로드합니다.

    ![예제 포장용 레이블](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)

4. 배송 상자를 봉인하고 반송 포장용 레이블이 식별 가능한지 확인합니다.
5. 미국에서 디바이스를 반환하는 경우 UPS로 픽업을 예약합니다. DHL로 유럽에서 디바이스를 반환하는 경우 해당 웹 사이트를 방문하고 항공 화물 운송장 번호를 지정하여 DHL에서 픽업을 요청합니다. 국가 DHL Express 웹 사이트로 이동하고 **Courier 컬렉션 예약 > eReturn 배송**을 선택합니다.

    ![DHL 반송 배송](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
    운송장 번호를 지정하고 **픽업 예약**을 클릭하여 픽업을 준비합니다.

      ![픽업 예약](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

7. 운송업체에서 디스크를 픽업하면 포털에서 주문 상태는 **픽업됨**으로 업데이트됩니다. 추적 ID도 표시됩니다.

    ![디스크 픽업됨](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

Microsoft에서 디스크를 수신하고 검사할 때 작업 상태는 **수신됨**으로 업데이트됩니다. 

![디스크 수신됨](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

디스크가 Azure 데이터 센터에서 서버에 연결되면 데이터는 자동으로 복사됩니다. 데이터 크기에 따라 복사 작업을 완료하는 데 몇 시간에서 몇 일이 걸릴 수 있습니다. 포털에서 복사 작업 진행 상황을 모니터링할 수 있습니다.

복사가 완료되면 주문 상태는 **완료됨**으로 업데이트됩니다.

![데이터 복사 완료됨](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

원본에서 데이터를 삭제하기 전에 데이터 저장소 계정에 있는지 확인합니다. 다음 위치에 데이터가 있을 수 있습니다.

- Azure Storage 계정. Data Box에 데이터를 복사할 때, 데이터 형식에 따라 Azure Storage 계정에서 다음 경로 중 하나에 업로드됩니다.

  - 블록 Blob 및 페이지 Blob의 경우: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files의 경우: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    또는 Azure Portal에서 Azure storage 계정으로 이동하여 찾아볼 수도 있습니다.

- 관리 디스크 리소스 그룹. 관리 디스크를 만들 때 VHD는 페이지 Blob으로 업로드된 다음, 관리 디스크로 변환됩니다. 관리 디스크는 주문 생성 시 지정된 리소스 그룹에 연결됩니다.

  - Azure에서 관리 디스크로 복사가 완료되면 Azure Portal에서 **주문 세부 정보**로 이동하여 관리 디스크에 지정된 리소스 그룹을 메모할 수 있습니다.

      ![주문 세부 정보 보기](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    메모한 리소스 그룹으로 이동하여 사용자의 관리 디스크를 찾습니다.

      ![관리 디스크의 리소스 그룹](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - VHDX 또는 동적/차이점 보관용 VHD를 복사한 경우 VHD/VHD가 준비 스토리지 계정에 블록 Blob으로 업로드됩니다. 준비 **Storage 계정 > Blob**으로 차례로 이동한 다음, 적절한 컨테이너(StandardSSD, StandardHDD 또는 PremiumSSD)를 선택합니다. VHDX/VHD는 준비 스토리지 계정에서 블록 Blob으로 표시되어야 합니다.

데이터가 Azure로 업로드되었는지 확인하려면 다음 단계를 수행합니다.

1. 디스크 주문과 연결된 저장소 계정으로 이동합니다.
2. **Blob service > Blob 찾아보기**로 이동합니다. 컨테이너 목록이 표시됩니다. *BlockBlob* 및 *PageBlob* 폴더 아래에서 만든 하위 폴더에 해당하는 동일한 이름의 컨테이너가 저장소 계정에 만들어집니다.
    폴더 이름이 Azure 명명 규칙을 따르지 않는 경우 Azure에 대한 데이터 업로드가 실패합니다.

4. 전체 데이터 세트가 로드되었는지 확인하려면 Microsoft Azure Storage 탐색기를 사용합니다. 디스크 임대 주문에 해당하는 저장소 계정을 연결한 다음, Blob 컨테이너의 목록을 살펴봅니다. 컨테이너를 선택하고, **...자세히**를 클릭한 다음, **폴더 통계**를 클릭합니다. **작업** 창에서 Blob의 수 및 총 Blob 크기를 포함하는 해당 폴더에 대한 통계가 표시됩니다. 총 Blob 크기(바이트)는 데이터 세트의 크기와 일치해야 합니다.

    ![Storage 탐색기에서 폴더 통계](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Data Box Disk에서 데이터 지우기

복사가 완료되고 데이터가 Azure 저장소 계정에 있는지 확인하면 디스크는 NIST 표준에 따라 안전하게 지워집니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box Disk 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * Microsoft에 Data Box Disk 배송
> * Azure에 대한 데이터 업로드 확인
> * Data Box Disk에서 데이터 지우기


Azure Portal을 통해 Data Box Disk를 관리하는 방법을 알아보려면 다음으로 진행합니다.

> [!div class="nextstepaction"]
> [Azure Portal을 사용하여 Azure Data Box Disk 관리](./data-box-portal-ui-admin.md)


