---
title: Microsoft Azure Data Box Disk FAQ | Microsoft Docs
description: 많은 양의 데이터를 Azure로 전송할 수 있는 클라우드 솔루션인 Azure Data Box Disk에 대해 자주 묻는 질문과 대답이 포함되어 있습니다.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/17/2018
ms.author: alkohli
ms.openlocfilehash: 5288e9900c75eae7601b84f7366edf9ac739d5da
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125806"
---
# <a name="what-is-azure-data-box-disk-preview"></a>Azure Data Box Disk란? (미리 보기)

Microsoft Azure Data Box Disk 클라우드 솔루션을 사용하면 테라바이트 단위의 데이터를 빠르고 저렴하게 신뢰할 수 있는 방식으로 Azure로 보낼 수 있습니다. 이 FAQ에는 Azure Portal에서 Data Box Disk를 사용할 때 제기될 수 있는 질문과 대답이 포함되어 있습니다. 

질문과 대답은 다음과 같은 범주로 정렬됩니다.

- 서비스 정보
- 구성 및 연결 
- 상태 추적
- 데이터 마이그레이션 
- 데이터 확인 및 업로드 

> [!IMPORTANT]
> Data Box Disk는 미리 보기로 있습니다. 이 솔루션을 배포하기 전에 [미리 보기에 대한 Azure 서비스 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 검토하세요.

## <a name="about-the-service"></a>서비스 정보

### <a name="q-what-is-azure-data-box-service"></a>Q. Azure Data Box 서비스란? 
a.  Azure Data Box 서비스는 오프라인 데이터 수집을 위해 설계되었습니다. 이 서비스는 다양한 저장 용량의 데이터 전송에 맞게 조정된 일련의 제품을 관리합니다. 

### <a name="q-what-are-azure-data-box-disks"></a>Q. Azure Data Box Disk란?
a. Azure Data Box Disk를 사용하면 Azure에서 테라바이트 단위의 데이터를 빠르고 저렴하게 신뢰할 수 있는 방식으로 전송할 수 있습니다. Microsoft에서 1-5개의 디스크를 제공하며 최대 저장 용량은 35TB입니다. Azure Portal에서 Data Box 서비스를 통해 이러한 디스크를 쉽게 구성, 연결 및 잠금 해제할 수 있습니다.  

디스크는 Microsoft BitLocker 드라이브 암호화를 통해 암호화되고, 암호화 키는 SSL을 통해 Azure Portal에서 관리됩니다. 그런 다음, 고객의 서버에서 데이터를 복사합니다. 데이터 센터에서 Microsoft는 빠른 사설망 업로드 링크를 사용하여 데이터를 드라이브에서 클라우드로 마이그레이션하고 Azure에 업로드합니다.

### <a name="q-when-should-i-use-data-box-disks"></a>Q. Data Box Disk는 언제 사용해야 하나요?
a. Azure로 전송하려는 데이터가 35TB 이하이면 Data Box Disk를 사용하는 것이 좋습니다.

### <a name="q-what-is-the-price-of-data-box-disks"></a>Q. Data Box Disk 가격은 어떻게 되나요?
a. 미리 보기 동안에는 Data Box Disk를 추가 비용 없이 사용할 수 있습니다. 또한 배송료는 무료이지만, Azure 저장소에 대한 비용이 부과됩니다.

### <a name="q-how-do-i-get-data-box-disks"></a>Q. Data Box Disk를 얻으려면 어떻게 할까요? 
a.  Azure Data Box Disk를 얻으려면 먼저 [Data Box Disk 미리 보기](http://aka.ms/AzureDataBox)에 등록합니다. 다음으로, Azure Portal에 로그인하고 디스크에 대한 Data Box 주문을 만듭니다. 연락처 정보 및 알림 세부 정보를 제공합니다. 가용성을 기준으로 주문이 확정되면 디스크가 10일 이내에 배송됩니다.   

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>Q. 한 인스턴스에서 Data Box Disk를 통해 전송할 수 있는 최대 데이터 양은 어떻게 되나요?
a. 각각 8TB(사용 가능한 용량: 7TB) 크기의 5개 디스크에 대해 사용 가능한 최대 용량은 35TB입니다. 따라서 한 인스턴스에서 35TB의 데이터를 전송할 수 있습니다.  더 많은 데이터를 전송하려면 디스크를 더 많이 주문해야 합니다.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>Q. 내 지역에서 Data Box Disk를 사용할 수 있는지 확인하려면 어떻게 해야 할까요? 
a.  미리 보기 단계에 있는 동안에는 미국 및 유럽 연합의 모든 국가에서 Data Box Disk를 사용할 수 있습니다.  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>Q. Data Box Disk에 데이터를 저장할 수 있는 지역은 어떻게 되나요?
a. Data Box Disk는 미국, 유럽 서부 및 유럽 북부 내의 모든 지역에서 미리 보기로 지원됩니다. Azure 공용 클라우드 지역만 지원됩니다. Azure Government 또는 다른 소버린 클라우드는 지원되지 않습니다.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>Q. Data Box Disk에 문제가 발생하면 누구에게 연락해야 하나요?
a. Data Box Disk에 문제가 발생하면 [Data Box Disk 고객 지원팀](mailto:expresspodsupport@microsoft.com)에 문의하세요.

## <a name="configure-and-connect"></a>구성 및 연결
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>Q. 주문에서 Data Box Disk의 수를 지정할 수 있나요?
a.  아니요. 데이터 크기 및 디스크 가용성에 따라 8TB 디스크(최대 5개 디스크)를 얻을 수 있습니다.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>Q. Data Box Disk의 잠금을 해제하려면 어떻게 할까요? 
a.  Azure Portal에서 해당 Data Box Disk 주문, **장치 세부 정보**로 차례로 이동합니다. 지원 암호를 복사합니다. Azure Portal에서 Data Box Disk 잠금 해제 도구를 다운로드 및 추출하고, 디스크에 복사하려는 데이터가 있는 컴퓨터에서 *DataBoxDiskUnlock.exe*를 실행합니다. 지원 암호를 제공하여 디스크의 잠금을 해제합니다. 동일한 지원 암호를 사용하여 모든 디스크의 잠금을 해제할 수 있습니다.

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>Q. Linux 호스트 컴퓨터를 사용하여 데이터를 Data Box Disk에 연결하고 복사할 수 있나요?
a.  아니요. Windows 컴퓨터만 지원됩니다. 자세한 내용은 호스트 컴퓨터에 대한 [지원되는 운영 체제](data-box-disk-system-requirements.md) 목록을 참조하세요.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Q. 내 디스크가 발송되었지만 이제 이 주문을 취소하려고 합니다. 취소 단추를 사용할 수 없는 이유는 무엇인가요?
a.  디스크의 주문 이후 및 배송 이전에만 주문을 취소할 수 있습니다. 디스크가 발송되면 더 이상 주문을 취소할 수 없습니다. 미리 보기 시간 프레임에서 솔루션이 일반 공급될 때에는 변경될 수 있지만 디스크는 추가 비용 없이 반환할 수 있습니다. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>Q. 여러 개의 Data Box Disk를 호스트 컴퓨터에 연결하여 데이터를 전송할 수 있나요?
a. 예. 여러 개의 Data Box Disk를 동일한 호스트 컴퓨터에 연결하여 데이터를 전송할 수 있으며, 여러 복사 작업을 병렬로 실행할 수 있습니다.

## <a name="track-status"></a>상태 추적

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>Q. 주문을 확정했을 때부터 디스크를 반송할 때까지 디스크를 추적하려면 어떻게 할까요? 
a.  Azure Portal에서 Data Box Disk 주문의 상태를 추적할 수 있습니다. 주문을 만들 때 알림 이메일을 제공하라는 메시지도 표시됩니다. 이메일을 제공한 경우 주문의 모든 상태 변경 내용에 대한 알림을 이 이메일을 통해 받게 됩니다. 자세한 내용은 [알림 이메일을 구성하는 방법](data-box-portal-ui-admin.md#edit-notification-details)을 참조하세요.

### <a name="q-how-do-i-return-the-disks"></a>Q. 디스크를 반환하려면 어떻게 할까요? 
a.  Microsoft에서는 배송 패키지에 Data Box Disk와 함께 배송 레이블을 제공합니다. 레이블을 배송 상자에 부착하고 봉인된 패키지를 배송업체 지점에 위탁합니다. 레이블이 손상되거나 분실되었으면 **개요 > 배송 레이블 다운로드**로 차례로 이동하여 새 반송 레이블을 다운로드합니다.

## <a name="migrate-data"></a>데이터 마이그레이션

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>Q. Data Box Disk에 사용할 수 있는 최대 데이터 크기는 어떻게 되나요?  
a.  Data Box Disk 솔루션은 최대 5개의 디스크를 사용할 수 있으며, 사용 가능한 최대 용량은 35TB입니다. 디스크 자체는 8TB(7TB 사용 가능)입니다. 

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>Q. Data Box Disk에서 지원하는 블록 Blob 및 페이지 Blob에 대한 최대 크기는 어떻게 되나요? 
a.  최대 크기는 Azure Storage 제한으로 관리됩니다. 최대 블록 Blob 크기는 대략 4.768TiB이고, 최대 페이지 Blob 크기는 8TiB입니다. 자세한 내용은 [Azure Storage 확장성 및 성능 목표](../storage/common/storage-scalability-targets.md)를 참조하세요. 

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>Q. Data Box Disk의 데이터 전송 속도는 어떻게 되나요?
a. USB 3.0을 통해 연결된 디스크를 테스트했을 때 디스크 성능은 최대 430MB/초였습니다. 실제 속도는 사용된 파일 크기에 따라 달라집니다. 작은 파일의 경우 성능이 저하될 수 있습니다.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Q. 전송 중에 내 데이터가 안전하다는 것을 확인하려면 어떻게 할까요? 
a.  Data Box Disk는 BitLocker AES 128비트 암호화를 사용하여 암호화되며, 지원 암호는 Azure Portal에서만 사용할 수 있습니다. 계정 정보를 사용하여 Azure Portal에 로그인한 다음, 지원 암호를 얻습니다. Data Box Disk 잠금 해제 도구를 실행할 때 이 지원 암호를 제공합니다.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>Q. 데이터를 Data Box Disk에 복사하려면 어떻게 할까요? 
a.  Robocopy, Diskboss 또는 Windows 파일 탐색기 끌어서 놓기와 같은 SMB 복사 도구를 사용하여 데이터를 디스크에 복사합니다. 

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Q. 데이터 복사 속도를 높일 수 있는 팁이 있나요?
a.  복사 프로세스의 속도를 높이려면 다음을 수행합니다.

- 다중 데이터 복사 스트림을 사용합니다. 예를 들어 Robocopy에서는 다중 스레드 옵션을 사용합니다. 사용되는 정확한 명령에 대한 자세한 내용은 [자습서: Azure Data Box Disk에 데이터 복사 및 확인](data-box-disk-deploy-copy-data.md#copy-data-to-disks)을 참조하세요.
- 다중 세션을 사용합니다.
- 네트워크 공유를 통해 복사하는(네트워크 속도로 인해 제한될 수 있음) 대신, 디스크가 연결된 컴퓨터에 데이터가 로컬로 있는지 확인합니다.
- 복사 프로세스 전체에서 USB 3.0 이상을 사용하고 있는지 확인합니다. [USBView 도구](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview)를 다운로드하고 사용하여 컴퓨터에 연결된 USB 컨트롤러 및 USB 장치를 식별합니다.
- 데이터를 복사하는 데 사용되는 컴퓨터의 성능을 벤치마크합니다. [Bluestop FIO 도구](https://bluestop.org/fio/)를 다운로드하고 사용하여 서버 하드웨어의 성능을 벤치마크합니다.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>Q. 원본 데이터에 작은 파일(KB 또는 수 MB)이 있는 경우 데이터의 속도를 높이려면 어떻게 해야 할까요?
a.  복사 프로세스의 속도를 높이려면 다음을 수행합니다.

- 고속 저장소에 로컬 VHDx를 만들거나, HDD/SSD에 빈 VHD를 만듭니다(속도가 느림).
- VM에 탑재합니다.
- 파일을 VM의 디스크에 복사합니다.


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>Q. Data Box Disk에 여러 개의 저장소 계정을 사용할 수 있나요?
a.  아니요. Data Box Disk에는 현재 하나의 저장소 계정(범용 또는 클래식)만 지원됩니다. 핫 및 쿨 Blob은 모두 지원됩니다. 미리 보기 동안에는 Azure 공용 클라우드에서 미국, 유럽 서부 및 유럽 북부의 저장소 계정만 지원됩니다.

## <a name="verify-and-upload"></a>확인 및 업로드

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>Q. 디스크가 반송되면 Azure에서 내 데이터에 얼마나 빨리 액세스할 수 있나요? 
a.  데이터 복사에 대한 주문 상태가 완료됨으로 표시되면 데이터에 바로 액세스할 수 있습니다.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Q. 업로드되면 Azure에서 내 데이터가 어디에 있나요?
a.  *BlockBlob* 및 *PageBlob* 폴더 아래에 있는 데이터를 디스크에 복사하면, *BlockBlob* 및 *PageBlob* 폴더 아래의 각 하위 폴더에 대한 컨테이너가 Azure 저장소 계정에 만들어집니다. 파일을 *BlockBlob* 및 *PageBlob* 폴더 아래에 직접 복사한 경우 이러한 파일은 Azure Storage 계정 아래의 *$root* 기본 컨테이너에 있습니다. 

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Q. 방금 내 컨테이너에 대해 Azure 명명 요구 사항을 따르지 않았음을 알게 되었습니다. 이 경우 내 데이터가 Azure에 업로드되지 않나요?
a. 컨테이너 이름에 대문자가 있으면 자동으로 소문자로 변환됩니다. 이름이 다른 방식(특수 문자, 다른 언어 등)으로 준수되지 않으면 업로드가 실패합니다.

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>Q. 여러 Data Box Disk에 복사한 데이터를 확인하려면 어떻게 할까요?
a.  데이터 복사가 완료되면 *AzureImportExport* 폴더에 제공된 `AzureExpressDiskService.cmd`를 실행하여 유효성 검사를 위한 체크섬을 생성할 수 있습니다. 여러 개의 디스크가 있는 경우 디스크마다 명령 창을 열어 이 명령을 실행해야 합니다. 이 작업은 데이터 크기에 따라 시간이 오래 걸릴 수 있음에 유의하세요.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>Q. 디스크가 반환되면 내 데이터는 어떻게 되나요?
a.  Azure로의 데이터 복사가 완료되면 디스크의 데이터는 NIST SP 800-88 수정 1 지침에 따라 안전하게 지워집니다.  

### <a name="q-how-is-my-data-protected-during-transit"></a>Q. 전송 중인 내 데이터는 어떻게 보호되나요? 
a.  Data Box Disk는 AES-128 Microsoft BitLocker 암호화로 암호화됩니다. 모든 디스크의 잠금을 해제하고 데이터에 액세스하려면 단일 지원 암호가 필요합니다.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>Q. Data Box Disk에 더 많은 데이터를 추가하면 체크섬 유효성 검사를 다시 실행해야 하나요?
a. 예. 데이터의 유효성을 검사하기로 결정한 경우(권장)! 디스크에 데이터를 추가한 경우 유효성 검사를 다시 실행해야 합니다.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>Q. 모든 내 디스크를 사용하여 데이터를 전송했으며 더 많은 디스크를 주문해야 합니다. 주문을 빠르게 확정할 수 있는 방법이 있나요?
a. 이전 주문을 복제할 수 있습니다. 복제하는 경우 이전과 동일한 주문을 만들고, 주소, 연락처 및 알림 세부 정보를 입력할 필요 없이 주문 세부 정보를 편집할 수 있습니다. 



## <a name="next-steps"></a>다음 단계

- [Data Box 시스템 요구 사항](data-box-disk-system-requirements.md)을 검토합니다.
- [Data Box 제한](data-box-disk-limits.md)을 알아봅니다.
- Azure Portal에서 [Azure Data Box Disk](data-box-disk-quickstart-portal.md)를 빠르게 배포합니다.
