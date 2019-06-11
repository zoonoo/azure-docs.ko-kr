---
title: Microsoft Azure Data Box FAQ | Microsoft Docs
description: 많은 양의 데이터를 Azure로 전송할 수 있는 클라우드 솔루션인 Azure Data Box에 대해 자주 묻는 질문과 대답이 포함되어 있습니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 05/23/2019
ms.author: alkohli
ms.openlocfilehash: 18c6175ede1e8459d3a9ca60f7890cc43d75e5ba
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236288"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box: 질문과 대답

Microsoft Azure Data Box 하이브리드 솔루션을 사용하면 이전 디바이스를 통해 테라바이트 단위의 데이터를 빠르고 저렴하게 신뢰할 수 있는 방식으로 Azure로 보낼 수 있습니다. 이 FAQ에는 Azure Portal에서 Data Box를 사용할 때 제기될 수 있는 질문과 대답이 포함되어 있습니다. 

질문과 대답은 다음과 같은 범주로 정렬됩니다.

- 서비스 정보
- 디바이스 주문
- 구성 및 연결 
- 상태 추적
- 데이터 복사 
- 디바이스 배송
- 데이터 확인 및 업로드 
- 관리 연속성 지원

## <a name="about-the-service"></a>서비스 정보

### <a name="q-what-is-azure-data-box-service"></a>Q. Azure Data Box 서비스란? 
a.  Azure Data Box 서비스는 오프라인 데이터 수집을 위해 설계되었습니다. 이 서비스는 다양한 저장 용량의 데이터 전송에 맞게 조정된 일련의 제품을 관리합니다. 

### <a name="q-what-is-azure-data-box"></a>Q. Azure Data Box란?
a. Azure Data Box를 사용하면 테라바이트 단위의 데이터를 빠르고 저렴하게 신뢰할 수 있는 방식으로 Azure로 전송할 수 있습니다. Azure Portal을 통해 Data Box 디바이스를 주문합니다. Microsoft가 지역 이동 통신 사업자를 통해 80TB용량의 사용 가능한 저장 디바이스를 배송합니다. 

디바이스를 받게 되면 로컬 웹 UI를 사용하여 신속하게 설정합니다. 서버에서 디바이스로 데이터를 복사하고 디바이스를 다시 Azure에 배송합니다. Azure 데이터 센터에서 사용자의 데이터는 디바이스에서 Azure로 자동으로 업로드됩니다. 전체 프로세스는 Azure Portal의 Data Box 서비스에 의해 엔드투엔드 추적됩니다.

### <a name="q-when-should-i-use-data-box"></a>Q. Data Box는 언제 사용해야 하나요?
a. Azure로 전송할 데이터가 40~500TB이면 Data Box를 사용하는 것이 유리합니다. 데이터 크기가 40TB 미만이면 Data Box Disk를 사용하고, 500TB가 넘으면 [Data Box Heavy](data-box-heavy-overview.md)에 가입합니다.

### <a name="q-what-is-the-price-of-data-box"></a>Q. Data Box 가격은 어떻게 되나요?
a. Data Box는 10일에 대한 명목 요금으로 사용할 수 있습니다. Azure Portal에서 주문 중에 제품 모델을 선택하면 디바이스 요금이 표시됩니다. 또한 배송료는 무료이지만, Azure 저장소에 대한 비용이 부과됩니다. 자세한 내용은 [Azure Data Box 가격 책정](https://azure.microsoft.com/pricing/details/storage/databox/)을 참조하세요. 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>Q. 한 인스턴스에서 Data Box를 통해 전송할 수 있는 최대 데이터 양은 어떻게 되나요?
a. Data Box의 원시 용량은 100TB, 사용 가능한 용량은 80TB입니다. Data Box를 사용하여 최대 80TB의 데이터를 전송할 수 있습니다. 더 많은 데이터를 전송하려면 디바이스를 더 주문해야 합니다.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>Q. 내 지역에서 Data Box를 사용할 수 있는지 확인하려면 어떻게 해야 할까요? 
a.  Data Box를 사용할 수 있는 국가/지역에 대한 내용은 [지역 가용성](data-box-overview.md#region-availability)을 참조하세요.  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>Q. Data Box에 데이터를 저장할 수 있는 지역은 어떻게 되나요?
a. Data Box는 미국, 유럽 서부, 유럽 북부, 프랑스 및 영국 내의 모든 지역에서 지원됩니다. Azure 공용 클라우드 지역만 지원됩니다. Azure Government 또는 다른 소버린 클라우드는 지원되지 않습니다. 자세한 내용은 [지역 가용성](data-box-overview.md#region-availability)을 참조하세요.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues-with-data-box"></a>Q. Data Box에 문제가 발생하면 어디로 연락해야 하나요?
a. Data Box에 문제가 발생하면 [Microsoft 지원](data-box-disk-contact-microsoft-support.md)에 문의하세요.

### <a name="q-i-have-lost-my-data-box-is-there-a-lost-device-charge"></a>Q. Data Box를 분실했습니다. 디바이스를 분실하면 요금이 청구되나요?
a. 예. 디바이스를 분실하거나 손상될 경우 요금이 청구됩니다. 이 요금은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/databox/)와 [제품 서비스 약관](https://www.microsoft.com/licensing/product-licensing/products)에 설명되어 있습니다.


## <a name="order-device"></a>디바이스 주문

### <a name="q-how-do-i-get-data-box"></a>Q. Data Box를 얻으려면 어떻게 할까요? 
a.  Azure Data Box를 가져오려면 Azure Portal에 로그인하고 Data Box 주문을 만듭니다. 연락처 정보 및 알림 세부 정보를 제공합니다. 주문 후에는 가용성에 따라 10일 안에 Data Box가 배송됩니다. 자세한 내용은 [Data Box 주문](data-box-deploy-ordered.md)을 참조하세요.

### <a name="q-i-was-not-able-to-create-a-data-box-order-in-the-azure-portal-why-would-this-be"></a>Q. Azure Portal에서 Data Box 주문을 만들 수 없습니다. 왜 그럴까요?
a. Data Box 주문을 만들 수 없는 경우 구독 유형이나 액세스에 문제가 있는 것입니다. 

구독을 확인합니다. Data Box는 EA(기업계약), CSP(클라우드 솔루션 공급자) 구독 제품에서만 사용할 수 있습니다. 구독이 이 유형에 해당하지 않는다면 Microsoft 지원에 문의하여 구독을 업그레이드합니다.

구독에 대해 지원되는 제안 유형이 있는 경우 구독 액세스 수준을 확인하세요. 주문을 만들려면 구독에서 기여자 또는 소유자여야 합니다.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-am-not-able-to-create-any-additional-orders-why-would-this-be"></a>Q. Data Box 디바이스를 여러 개 주문했습니다. 다른 주문을 더 만들 수 없습니다. 왜 그럴까요?
a. 상거래 범위(선택한 국가 및 지역 조합)당 구독별로 최대 5개의 활성 주문이 가능합니다. 디바이스를 더 주문하려면 Microsoft 지원에 문의하여 구독 한도를 늘리세요.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>Q. 주문을 만들 때 Data Box 서비스를 사용할 수 없다는 알림을 받았습니다. 무슨 의미인가요?
a. 선택한 국가와 지역 조합에서는 Data Box 서비스를 사용할 수 없다는 의미입니다. 이 조합을 변경하면 Data Box 서비스가 제공될 수도 있습니다. 서비스를 사용할 수 있는 지역 목록은 [Data Box에 대한 지역 가용성](data-box-overview.md#region-availability)을 참조하세요.

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>Q. 며칠 전 Data Box를 주문했습니다. 언제 Data Box를 받게 될까요?
a. 주문이 이루어지면 해당 주문에 디바이스가 가능한지 여부를 확인합니다. 디바이스가 가능하다면 10일 안에 출고합니다. 수요가 몰리는 기간이 있을 수 있습니다. 이런 경우에는 주문이 대기 상태가 되고 Azure Portal에서 상태 변경을 추적할 수 있습니다. 주문이 90일 안에 이행되지 않으면 자동으로 취소됩니다.

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>Q. Data Box에 데이터가 가득 차 다른 장치를 더 주문해야 합니다. 주문을 빠르게 확정할 수 있는 방법이 있나요?
a. 이전 주문을 복제할 수 있습니다. 복제하는 경우 이전과 동일한 주문을 만들고, 주소, 연락처 및 알림 세부 정보를 입력할 필요 없이 주문 세부 정보를 편집할 수 있습니다.

## <a name="configure-and-connect"></a>구성 및 연결

### <a name="q-how-do-i-unlock-the-data-box"></a>Q. Data Box의 잠금을 해제하려면 어떻게 할까요? 
a.  Azure Portal에서 해당 Data Box 주문, **디바이스 세부 정보**로 차례로 이동합니다. 잠금 해제 암호를 복사합니다. 이 암호를 사용하여 Data Box에서 로컬 웹 UI에 로그인합니다. 자세한 내용은 [자습서: Azure Data Box 포장 해제, 케이블 연결, 연결](data-box-deploy-set-up.md)을 참조하세요.

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>Q. Linux 호스트 컴퓨터를 사용하여 Data Box에 연결하고 데이터를 복사할 수 있나요?
a.  예. Data Box를 사용하여 SMB 및 NFS 클라이언트에 연결할 수 있습니다. 자세한 내용은 호스트 컴퓨터에 대한 [지원되는 운영 체제](data-box-system-requirements.md) 목록을 참조하세요.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Q. 내 Data Box가 발송되었지만 이제 이 주문을 취소하려고 합니다. 취소 단추를 사용할 수 없는 이유는 무엇인가요?
a.  Data Box를 주문한 후 주문이 처리되기 전에만 주문을 취소할 수 있습니다. Data Box 주문이 처리되면 더 이상 주문을 취소할 수 없습니다. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>Q. Data Box를 한 번에 여러 호스트 컴퓨터에 연결하여 데이터를 전송할 수 있나요?
a. 예. 여러 호스트 컴퓨터를 동일한 Data Box에 연결하여 데이터를 전송할 수 있으며, 여러 복사 작업을 병렬로 실행할 수 있습니다. 자세한 내용은 [자습서: Azure Data Box에 데이터 복사](data-box-deploy-copy-data.md)를 참조하세요.

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>Q. Data Box의 두 10GbE 인터페이스에 연결하여 데이터를 전송할 수 있나요?
a. 예. Data Box의 두 10GbE 인터페이스에 모두 연결하여 동시에 데이터를 복사할 수 있습니다. 데이터를 복사하는 방법에 대한 자세한 내용은 [자습서: Azure Data Box에 데이터 복사](data-box-deploy-copy-data.md)를 참조하세요.

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why would this be?
A.

### Q. I could not set up Data Box using a Static IP address. Why would this be?
A.

### Q. I could not set up Data Box on a private network. Why would this be?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>Q. 전면 작동 패널의 시스템 결함 표시기 LED가 켜졌습니다. 어떻게 해야 하나요?
a. 시스템 결함 표시기 LED가 켜져 있으면 시스템이 정상 상태가 아님을 나타냅니다. [Microsoft 지원에 문의](data-box-disk-contact-microsoft-support.md)하세요.

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why-would-this-be"></a>Q. Azure Portal에서 Data Box 잠금 해제 암호에 액세스할 수 없습니다. 왜 그럴까요?
a. Azure Portal의 잠금 해제 암호에 액세스할 수 없다면 구독 및 저장소 계정의 권한을 확인합니다. 리소스 그룹 수준에서 기여자 또는 소유자 권한이 있는지 확인합니다. 없는 경우 액세스 자격 증명을 보려면 최소한 Data Box 운영자 역할 권한이 있어야 합니다.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>Q. Data Box에서 포트 채널 구성이 지원되나요? MPIO는 어떤가요?
a. 포트 채널 구성, MPIO(다중 경로 IO) 구성 또는 vLAN 구성은 Data Box에 지원되지 않습니다.

## <a name="track-status"></a>상태 추적

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>Q. 주문을 확정했을 때부터 Data Box를 반송할 때까지 디바이스를 추적하려면 어떻게 할까요? 
a.  Azure Portal에서 Data Box 주문의 상태를 추적할 수 있습니다. 주문을 만들 때 알림 이메일을 제공하라는 메시지도 표시됩니다. 이메일을 제공한 경우 주문의 모든 상태 변경 내용에 대한 알림을 이 이메일을 통해 받게 됩니다. 자세한 내용은 [알림 이메일을 구성하는 방법](data-box-portal-ui-admin.md#edit-notification-details)을 참조하세요.

### <a name="q-how-do-i-return-the-device"></a>Q. 디바이스를 반환하려면 어떻게 할까요? 
a.  Microsoft는 E 잉크 디스플레이에 배송 레이블을 표시합니다. E 잉크 디스플레이에 배송 레이블이 보이지 않으면 **개요 > 배송 레이블 다운로드**로 이동합니다. 레이블을 다운로드하여 인쇄하고, 디바이스의 빈 플라스틱 태그에 레이블을 삽입하고, 디바이스를 배송업체에 맡깁니다. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>Q. 디바이스가 Azure 데이터 센터에 도착했다는 이메일 알림을 받았습니다. 데이터 업로드가 진행 중인지 어떻게 확인하나요?
a. Azure Portal에서 Data Box 주문, **개요**로 차례로 이동할 수 있습니다. Azure에 대한 데이터 업로드가 시작되었다면 오른쪽 창에 복사 진행 상황이 표시됩니다. 

## <a name="migrate-data"></a>데이터 마이그레이션

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>Q. Data Box에 사용할 수 있는 최대 데이터 크기는 어떻게 되나요?  
a.  Data Box에는 80TB의 사용 가능한 저장소 용량이 있습니다. 크기가 40 ~ 80TB에 해당하는 데이터에는 단일 Data Box 디바이스를 사용할 수 있습니다. 최대 500TB의 데이터 크기가 필요한 경우 여러 개의 Data Box 디바이스를 주문할 수 있습니다. 500TB가 넘는 데이터는 Data Box Heavy에 가입하세요.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>Q. Data Box에서 지원하는 블록 Blob 및 페이지 Blob에 대한 최대 크기는 어떻게 되나요? 
a.  최대 크기는 Azure Storage 제한으로 관리됩니다. 최대 블록 Blob 크기는 대략 4.768TiB이고, 최대 페이지 Blob 크기는 8TiB입니다. 자세한 내용은 [Azure Storage 확장성 및 성능 목표](../storage/common/storage-scalability-targets.md)를 참조하세요. 

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Q. 전송 중에 내 데이터가 안전하다는 것을 확인하려면 어떻게 할까요? 
a. 운송 중 Data Box 보호를 위해 여러 보안 기능이 구현되어 있습니다. 여기에는 손상 증명 봉인, 하드웨어 및 소프트웨어 변조 탐지, 디바이스 잠금 해제 암호 등을 비롯한 여러 기능이 포함됩니다. 자세한 내용은 [Azure Data Box 보안 및 데이터 보호](data-box-security.md)를 참조하세요.

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>Q. Data Box에 어떻게 데이터를 복사하나요? 
a.  SMB 클라이언트를 사용할 경우 Robocopy, Diskboss 또는 Windows 파일 탐색기 끌어서 놓기와 같은 SMB 복사 도구를 사용하여 데이터를 디바이스에 복사합니다. 

NFS 클라이언트를 사용할 경우 [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) 또는 [Ultracopier](https://ultracopier.first-world.info/)를 사용할 수 있습니다. 

자세한 내용은 [자습서: Azure Data Box에 데이터 복사](data-box-deploy-copy-data.md)를 참조하세요.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Q. 데이터 복사 속도를 높일 수 있는 팁이 있나요?
a.  복사 프로세스의 속도를 높이려면 다음을 수행합니다.

- 다중 데이터 복사 스트림을 사용합니다. 예를 들어 Robocopy에서는 다중 스레드 옵션을 사용합니다. 사용되는 정확한 명령에 대한 자세한 내용은 [자습서: Azure Data Box에 데이터 복사 및 확인](data-box-deploy-copy-data.md)을 완료했는지 확인합니다.
- 다중 세션을 사용합니다.
- 네트워크 공유를 통해 복사하는(네트워크 속도로 인해 제한될 수 있음) 대신, Data Box가 연결된 컴퓨터에 데이터가 로컬로 있는지 확인합니다.
- 데이터를 복사하는 데 사용되는 컴퓨터의 성능을 벤치마크합니다. [Bluestop FIO 도구](https://bluestop.org/fio/)를 다운로드하고 사용하여 서버 하드웨어의 성능을 벤치마크합니다.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM’s disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>Q. Data Box에 여러 개의 저장소 계정을 사용할 수 있나요?
a.  예. Data Box에서는 최대 10개의 스토리지 계정, 범용, 클래식 또는 Blob Storage 계정이 지원됩니다. 핫 및 쿨 Blob은 모두 지원됩니다. GA 릴리스 중에는 Azure 공용 클라우드에서 미국, 유럽 서부, 유럽 북부, 프랑스 및 영국의 모든 지역의 저장소 계정이 지원됩니다.


## <a name="ship-device"></a>디바이스 배송

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>Q. 내 디바이스가 배송되었으나 손상된 것 같습니다. 어떻게 해야 하나요?
a. 디바이스가 손상된 상태로 도착했거나 변조의 증거가 있다면 디바이스를 사용하지 않습니다. [Microsoft 지원에 문의](data-box-disk-contact-microsoft-support.md)하고 가능한 빨리 디바이스를 반환하세요. 교체 디바이스를 위한 새 Data Box 주문을 만들 수도 있습니다. 이런 경우 교체 디바이스에 대한 요금이 청구되지 않습니다.

### <a name="q-can-i-use-my-own-shipping-carrier-to-ship-data-box"></a>Q. Data Box 배송에 내가 원하는 배송 업체를 사용할 수 있나요?
a. Data Box 서비스에서는 Microsoft가 Azure 데이터 센터로의 배송을 처리합니다. 사용자 고유의 배송 업체를 사용하려면 Azure Import/Export 서비스를 사용할 수 있습니다. 자세한 내용은 [Azure Import/Export service 란?](../storage/common/storage-import-export-service.md)을 참조하세요.

### <a name="q-will-my-data-box-devices-cross-country-borders-during-shipping"></a>Q. 배송 중에 내 Data Box 디바이스가 국가 경계를 넘을 수 있나요?
a. 모든 Data Box 디바이스는 목적지와 동일한 국가 내에서 배송되며 국경을 넘지 않습니다. 유일한 예외는 디바이스가 모든 EU 국가 간에 발송될 수 있는 EU(유럽 연합) 내에서의 주문입니다. Data Box와 Data Box Heavy 디바이스에 모두 적용됩니다.

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>Q. E 잉크 표시가 반송 레이블에 없습니다. 어떻게 해야 하나요?
a. 반송 레이블에 E 잉크가 표시되지 않으면 다음 단계를 수행합니다.
- 기존 배송 레이블과 스티커를 모두 이전 배송에서 제거합니다.
- Azure Portal에서 주문으로 이동합니다. **개요**로 이동하여 **배송 레이블을 다운로드**합니다. 자세한 내용은 [배송 레이블 다운로드](data-box-portal-admin.md#download-shipping-label)를 참조하세요.
- 배송 레이블을 인쇄하고, 디바이스에 부착된 빈 플라스틱 슬리브에 삽입합니다. 
- 배송 레이블이 분명히 보이게 합니다. 

### <a name="q-how-is-my-data-protected-during-transit"></a>Q. 전송 중인 내 데이터는 어떻게 보호되나요? 
a.  운송 중에는 Data Box의 다음 기능을 통해 데이터를 보호합니다.
 - Data Box 디스크는 AES 256비트 암호화로 암호화됩니다. 
 - 디바이스가 잠겨 있으며 잠금 해제 암호가 있어야 들어가 데이터에 액세스할 수 있습니다.
자세한 내용은 [Data Box 보안 기능](data-box-security.md)을 참조하세요.  

### <a name="q-i-have-finished-prepare-to-ship-and-shut-down-the-device-can-i-still-add-more-data-to-data-box"></a>Q. 배송 준비를 마치고 디바이스를 종료했습니다. Data Box에 데이터를 추가할 수 있나요?
a. 예. 디바이스를 켜고 데이터를 더 추가할 수 있습니다. 데이터 복사를 마친 후에는 **배송 준비**를 다시 실행해야 합니다.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>Q. 내 디바이스를 받았는데 부팅되지 않았습니다. 디바이스를 다시 배송하려면 어떻게 해야 하나요?
a. 디바이스가 부팅되지 않는 경우 Azure Portal에서 주문 내역으로 이동합니다. 배송 레이블을 다운로드하고 디바이스에 부착합니다. 자세한 내용은 [배송 레이블 다운로드](data-box-portal-admin.md#download-shipping-label)를 참조하세요.

## <a name="verify-and-upload"></a>확인 및 업로드

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>Q. Data Box를 보낸 뒤 언제쯤 Azure에서 내 데이터에 액세스할 수 있나요? 
a.  **데이터 복사**에 대한 주문 상태가 **완료됨**으로 표시되면 데이터에 바로 액세스할 수 있습니다.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Q. 업로드되면 Azure에서 내 데이터가 어디에 있나요?
a.  Data Box에 데이터를 복사할 때 데이터가 페이지 Blob인지, 블록 Blob인지 아니면 Azure 파일인지에 따라 Azure Storage 계정에서 다음 경로 중 하나에 업로드됩니다.
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  또는 Azure Portal에서 Azure storage 계정으로 이동하여 찾아볼 수도 있습니다.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Q. 방금 내 컨테이너에 대해 Azure 명명 요구 사항을 따르지 않았음을 알게 되었습니다. 이 경우 내 데이터가 Azure에 업로드되지 않나요?
a.  컨테이너 이름에 대문자가 있으면 자동으로 소문자로 변환됩니다. 이름이 다른 방식(특수 문자, 다른 언어 등)으로 준수되지 않으면 업로드가 실패합니다. 공유, 컨테이너, 파일 이름 지정의 모범 사례에 대한 자세한 내용은 다음을 참조하세요.
- [공유 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [블록 Blob 및 페이지 Blob 규칙](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>Q. Data Box에 복사한 데이터는 어떻게 확인하나요?
a.  데이터 복사가 완료되면 **배송 준비**를 실행할 때 데이터의 유효성을 검사합니다. Data Box는 유효성 검사 프로세스 중에 파일 목록과 데이터에 대한 체크섬을 만듭니다. 파일 목록을 다운로드하고 원본 데이터의 파일과 대조 확인할 수 있습니다. 자세한 내용은 [배송 준비](data-box-deploy-picked-up.md#prepare-to-ship)를 참조하세요.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-data-box"></a>Q. Data Box를 반환한 후 내 데이터는 어떻게 되나요?
a.  Azure로 데이터 복사가 완료되면 Data Box 디스크의 데이터는 NIST SP 800-88 수정 1 지침에 따라 안전하게 지워집니다. 자세한 내용은 [Data Box에서 데이터 지우기](data-box-deploy-picked-up.md#erasure-of-data-from-data-box)를 참조하세요.

## <a name="audit-report"></a>감사 보고서

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Azure Data Box 서비스가 고객 관리 연속성 절차에 어떻게 도움이 되나요?
a.  Azure Data Box 서비스는 기본적으로 관리 연속성 문서에 사용할 수 있는 보고서를 제공합니다. 이 감사 및 복사 로그는 Azure의 스토리지 계정에서 제공되며, 주문이 완료되면 Azure Portal에서 [주문 기록을 다운로드](data-box-portal-admin.md#download-order-history)할 수 있습니다.


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>관리 연속성 지원에 사용 가능한 보고서의 유형은 무엇인가요?
a.  관리 연속성 지원을 위해 다음 보고를 사용할 수 있습니다.

- UPS로부터의 물류 운송
- 전원 켜기 및 사용자 공유 액세스 로깅
- Data Box에 성공적으로 수집된 각 파일에 대 한 64비트 CRC-64(순환 중복 검사) 또는 체크섬이 적용된 매니페스트 파일
- Azure Storage 계정에 업로드하지 못한 파일 보고
- 데이터가 Azure Storage 계정에 복사된 후 Data Box 디바이스 삭제(NIST 800 88R1 표준에 따라)

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>운송업체 추적 로그(UPS 제공)를 사용할 수 있나요? 
a.  이동 통신 사업자 추적 로그는 Data Box 주문 기록에 캡처됩니다. 이 보고서는 디바이스가 Azure 데이터 센터로 반환되고 디바이스 디스크의 데이터가 정리된 후 사용할 수 있습니다. 즉시 필요한 경우 배송 업체 웹 사이트에서 직접 주문 추적 번호를 사용하여 추적 정보를 구할 수 있습니다.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Azure 데이터 센터에 Data Box를 직접 전달할 수 있나요? 
a.  아니요. 현재 Azure 데이터 센터는 고객 또는 UPS 이외의 운송업체에서 수행하는 Data Box 배달을 허용하지 않습니다.


## <a name="next-steps"></a>다음 단계

- [Data Box 시스템 요구 사항](data-box-system-requirements.md)을 검토합니다.
- [Data Box 제한](data-box-limits.md)을 알아봅니다.
- Azure Portal에서 [Azure Data Box](data-box-quickstart-portal.md)를 빠르게 배포합니다.
