---
title: Azure Data Box Disk 반송 자습서 | Microsoft Docs
description: 이 자습서를 사용하여 Microsoft에 Azure Data Box Disk를 배송하는 방법 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 8df9c2a5e45cf222debc733b3e051c3f707715c5
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65603094"
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
2. 완충재를 사용하여 모든 디스크 및 연결 케이블을 래핑하고 배송 상자에 배치합니다. 액세서리가 없는 경우 요금이 부과될 수 있습니다.
    - 최초 배송의 포장재를 다시 사용합니다.  
    - 안전하게 보호하는 공기 쿠션 랩을 사용하여 디스크를 포장하는 것이 좋습니다.
    - 상자 내의 움직임을 줄이기 위해 빈틈 없이 채워졌는지 확인합니다.

다음 단계는 디바이스를 반송하는 위치에 따라 결정됩니다.

### <a name="pick-up-in-us-canada"></a>미국, 캐나다에서 픽업

미국 또는 캐나다에서 디바이스를 반송하는 경우 다음 단계를 수행합니다.

1. 상자에 부착된 투명 플라스틱 케이스에서 반송 포장용 레이블을 사용합니다. 레이블이 손상되었거나 손실된 경우 다음을 수행합니다.
    - **개요 > 포장용 레이블 다운로드**로 이동합니다.

        ![포장용 레이블 다운로드](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        이 작업은 아래 표시된 것처럼 반송 포장용 레이블을 다운로드합니다.

        ![예제 포장용 레이블](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - 디바이스에 레이블을 부착합니다.

2. 배송 상자를 봉인하고 반송 포장용 레이블이 식별 가능한지 확인합니다.
3. UPS를 사용하여 픽업을 예약합니다. 픽업을 예약하려면 다음을 수행합니다.

    - 로컬 UPS(국가/지역별 무료 전화 번호)에 전화합니다.
    - 통화에서 인쇄된 레이블에 표시된 대로 역방향 배송 추적 번호를 알려줍니다.
    - 추적 번호를 알려주지 않으면 픽업 시 UPS에서 추가 요금을 지불하도록 요구합니다.
    - 픽업을 예약하는 대신 가장 가까운 반납 위치에 Data Box Disk를 반납할 수도 있습니다.


### <a name="pick-up-in-europe"></a>유럽에서 픽업

유럽에서 디바이스를 반송하는 경우 다음 단계를 수행합니다.

1. 상자에 부착된 투명 플라스틱 케이스에서 반송 포장용 레이블을 사용합니다. 레이블이 손상되었거나 손실된 경우 다음을 수행합니다.
    - **개요 > 포장용 레이블 다운로드**로 이동합니다.

        ![포장용 레이블 다운로드](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        이 작업은 아래 표시된 것처럼 반송 포장용 레이블을 다운로드합니다.

        ![예제 포장용 레이블](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - 디바이스에 레이블을 부착합니다.

2. 배송 상자를 봉인하고 반송 포장용 레이블이 식별 가능한지 확인합니다.
3. DHL로 유럽에서 디바이스를 반환하는 경우 해당 웹 사이트를 방문하고 항공 화물 운송장 번호를 지정하여 DHL에서 픽업을 요청합니다.
4. 국가/지역 DHL Express 웹 사이트로 이동하고 **Courier 컬렉션 예약 > eReturn 배송**을 선택합니다.

    ![DHL 반송 배송](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. 운송장 번호를 지정하고 **픽업 예약**을 클릭하여 픽업을 준비합니다.

      ![픽업 예약](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>아시아 태평양 지역에서 픽업

이 지역에는 일본, 한국, 오스트레일리아에서의 픽업 지침이 포함되어 있습니다.

#### <a name="pick-up-in-australia"></a>오스트레일리아에서 픽업

오스트레일리아의 Azure 데이터 센터에는 추가 보안 알림이 있습니다. 모든 인바운드 배송에는 고급 알림이 있어야 합니다. 오스트레일리아에서 픽업하려면 다음 단계를 수행합니다.

1. 고유한 인바운드 ID 또는 TAU 코드가 있는 배송 레이블을 요청하는 이메일을 `adbops@microsoft.com`에 보냅니다. 레이블을 제 시간에 맞춰 받으려면 계획한 배송 날짜보다 최소 3일 전에 요청합니다.
2. 이메일 제목은 *TAU 코드가 있는 역방향 배송 레이블 요청*이어야 합니다. 이메일에 포함되는 세부 정보는 다음과 같습니다. 

    - 주문 이름
    - 주소
    - 담당자 이름

#### <a name="pick-up-in-japan"></a>일본에서 픽업

1. Japan Post Chakubarai의 반송 connote가 포함되어야 합니다.
2. 회사 이름과 주소 정보를 보낸 사람 정보로 connote에 작성합니다.
3. Japan Post 픽업 요청 번호인 0800-0800-111(무료 전화)로 전화합니다. 픽업 주소에 대한 7자리 우편 번호를 누른 다음, 가까운 우체국으로 전달합니다.
    - 픽업 요청에 사용 가능한 시간은 각 우체국에 따라 다릅니다.
    - Japan Post Chakubarai Yu-Pack으로 보내는 배송임을 알려줍니다.
    - 포함된 Chakubarai connote를 사용합니다.
4. Japan Post Chakubarai connote가 포함되지 않으면 이메일을 `Customerservice.JP@quantiumsolutions.com`의 *Quantum Solutions*에 보냅니다. *Quantium Solutions*에서 Japan Post에 픽업을 요청하고, 픽업 시 connote를 가져오도록 요청합니다.
    - Japan Post에서 가져올 Chakubarai connote 비고란에 참조 번호를 기재합니다.
    - 아래와 같은 배송 주소를 입력합니다.   
        ```
        3F N7 Prologis Park Tokyo Ohta, 1-3-6 Tokai Ohta-ku, Tokyo 143-0001
        Microsoft Service Center c/o Quantium Solutions Japan
        TEL: 03-5755-0150
        ```

Chakubarai connote가 없으면 이메일을 통해 픽업을 요청할 수 있습니다. 이 픽업을 요청하려면 다음 이메일 템플릿을 사용합니다.

```
To: Customerservice.JP@quantiumsolutions.com
Subject: Pickup request for Azure Data Box Disk｜Job Name： 
Body: 
- Azure Data Box Disk job name：
- Reference number:  
- Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
```

#### <a name="pick-up-in-korea"></a>한국에서 픽업

1. 반송 connote가 포함되어야 합니다.
2. 픽업을 요청하려면 다음을 수행합니다.
    1. 근무 시간(오전 10시~오후 5시, 월요일~금요일)에는 *Quantium Solutions International*의 핫라인 070-8231-1418로 전화합니다. *Microsoft 픽업* 및 connote 번호를 인용하여 수집물을 준비합니다.  
    2. 핫라인이 사용 중인 경우 *Microsoft 픽업*이라는 이메일 제목과 connote 번호가 포함된 이메일을 `microsoft@rocketparcel.com`에 보냅니다.
    3. 택배 회사에서 수집을 위해 도착하지 않으면 *Quantium Solutions International* 핫라인으로 전화를 걸어 대체 예약을 요청합니다. 

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

운송업체에서 디스크를 픽업하면 포털에서 주문 상태는 **픽업됨**으로 업데이트됩니다. 추적 ID도 표시됩니다.

![디스크 픽업됨](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

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


