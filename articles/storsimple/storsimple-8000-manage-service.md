---
title: Azure에서 StorSimple 디바이스 관리자 서비스 배포 | Microsoft Docs
description: 이 자습서에서는 서비스 만들기, 삭제, 마이그레이션 및 서비스 등록 키 관리에 필요한 단계를 알아봅니다.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 85d66b5e4f7e95d114cbf0b4c681d73eebb93a67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076567"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>StorSimple 8000 시리즈 디바이스에 StorSimple 디바이스 관리자 서비스 배포

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>개요

StorSimple Device Manager 서비스는 Microsoft Azure에서 실행되며 여러 StorSimple 디바이스에 연결됩니다. 서비스를 만든 후에 단일 중앙 위치에서 StorSimple 디바이스 관리자 서비스에 연결된 모든 디바이스를 관리하는 데 사용하여 관리 부담을 최소화할 수 있습니다.

이 자습서에서는 서비스 만들기, 삭제, 마이그레이션 및 서비스 등록 키 관리에 필요한 단계를 설명합니다. 이 문서에 포함된 정보는 StorSimple 8000 시리즈 디바이스에만 적용됩니다. StorSimple 가상 배열에 대한 자세한 내용은 [StorSimple 가상 배열에 StorSimple 디바이스 관리자 서비스 배포](storsimple-virtual-array-manage-service.md)로 이동합니다.

> [!NOTE]
> -  Azure Portal은 업데이트 5.0 이상을 실행하는 디바이스를 지원합니다. 디바이스가 최신 상태가 아니면 즉시 업데이트 5를 설치합니다. 자세한 내용은 [업데이트 5 설치](storsimple-8000-install-update-5.md)를 참조하세요. 
> - StorSimple Cloud Appliance(8010/8020)를 사용하는 경우 클라우드 어플라이언스를 업데이트할 수 없습니다. 최신 버전의 소프트웨어를 사용하여 업데이트 5.0으로 새 클라우드 어플라이언스를 만든 다음 만들어진 새 클라우드 어플라이언스로 장애 조치(failover)합니다. 
> - 업데이트 4.0 이하를 실행하는 디바이스는 관리 기능이 축소됩니다. 

## <a name="create-a-service"></a>서비스 만들기
StorSimple 디바이스 관리자 서비스를 만들려면 다음 항목이 필요합니다.

* 엔터프라이즈 계약을 사용하여 구독
* 활성 Microsoft Azure Storage 계정
* 액세스 관리에 사용되는 청구 정보

기업 규약을 포함하는 구독만이 허용됩니다. 또한 서비스를 만들 때 기본 스토리지 계정을 생성하도록 선택할 수 있습니다.

하나의 서비스로 여러 디바이스를 관리할 수 있습니다. 하지만 하나의 디바이스는 여러 서비스로 확장할 수 없습니다. 대규모 엔터프라이즈는 서로 다른 구독, 조직 또는 배포 위치와 동작하는 여러 서비스 인스턴스를 가질 수 있습니다. 

> [!NOTE]
> StorSimple 8000 시리즈 디바이스와 StorSimple 가상 배열을 관리하려면 별도의 StorSimple Device Manager 서비스 인스턴스가 필요합니다.

서비스를 만들려면 다음 단계를 수행합니다.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


각 StorSimple 디바이스 관리자 서비스에 대해 다음과 같은 특성이 존재합니다.

* **이름** – StorSimple 디바이스 관리자 서비스를 만들었을 때 할당된 이름입니다. **서비스를 만든 후에는 서비스 이름을 변경할 수 없습니다. 이는 Azure Portal에서 이름을 바꿀 수 없는 디바이스, 볼륨, 볼륨 컨테이너, 백업 정책과 같은 다른 엔터티의 경우에도 마찬가지입니다.**
* **상태** – 서비스의 상태로, **활성**, **만드는 중** 또는 **온라인** 일 수 있습니다.
* **위치** – StorSimple 디바이스를 배포할 지리적 위치입니다.
* **구독** – 서비스와 연관된 청구 구독입니다.

## <a name="delete-a-service"></a>서비스 삭제

서비스를 삭제하기 전에 사용 중인 연결 디바이스가 없는지 확인합니다. 서비스를 사용 중인 경우 연결된 디바이스를 비활성화합니다. 비활성화 작업은 디바이스와 서비스 간의 연결을 제공하지만 클라우드의 디바이스 데이터는 유지합니다.

> [!IMPORTANT]
> 서비스가 삭제된 후에는 작업을 되돌릴 수 없습니다. 서비스를 사용하던 모든 디바이스는 다른 서비스에 사용하기 전에 공장 기본값으로 다시 설정해야 합니다. 이 시나리오에서는 구성뿐 아니라 디바이스의 로컬 데이터도 손실됩니다.

서비스를 삭제하려면 다음 단계를 수행합니다.

### <a name="to-delete-a-service"></a>서비스를 삭제하려면

1. 삭제하려는 서비스를 검색합니다. **리소스** 아이콘을 클릭하고 검색하려는 적절한 용어를 입력합니다. 검색 결과에서 삭제하려는 서비스를 클릭합니다.

    ![삭제할 서비스 검색](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. 그러면 StorSimple 디바이스 관리자 서비스 블레이드로 이동됩니다. **삭제** 를 클릭합니다.

    ![서비스 삭제](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. 확인 알림에서 **예** 를 클릭합니다. 서비스 삭제에는 몇 분 정도 걸릴 수 있습니다.

    ![삭제 확인](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>서비스 등록 키 가져오기

서비스를 성공적으로 만든 후에 서비스에 StorSimple 디바이스를 등록해야 합니다. 처음으로 StorSimple 디바이스를 등록하려면 서비스 등록 키가 필요합니다. 기존 StorSimple 서비스에 추가 디바이스를 등록하려면 등록 키와 서비스 데이터 암호화 키(등록 시 첫 번째 디바이스에서 생성된 키)가 모두 필요합니다. 서비스 데이터 암호화 키에 대한 자세한 내용은 [StorSimple 보안](storsimple-8000-security.md)을 참조하세요. StorSimple 디바이스 관리자 블레이드의 **키** 에 액세스하여 등록 키를 가져올 수 있습니다.

서비스 등록 키를 가져오려면 다음 단계를 수행합니다.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

서비스 등록 키를 안전한 장소에 보관합니다. 이 키와 서비스 데이터 암호화 키는 이 서비스에 추가 디바이스를 등록할 때 필요합니다. 서비스 등록 키를 가져온 후 StorSimple용 Windows PowerShell 인터페이스를 통해 디바이스를 구성해야 합니다.

이 등록 키 사용 방법에 대한 세부 정보는 [3단계: StorSimple용 Windows PowerShell을 통해 디바이스 구성 및 등록](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)을 참조하세요.

## <a name="regenerate-the-service-registration-key"></a>서비스 등록 키 생성
키 회전을 수행해야 하거나 서비스 관리자 목록이 변경된 경우 서비스 등록 키를 다시 생성해야 합니다. 키를 다시 생성하면 후속 디바이스 등록을 위해서만 새 키가 사용됩니다. 이미 등록된 디바이스는 이 과정에 영향을 받지 않습니다.

서비스 등록 키를 다시 생성하려 다음 단계를 수행합니다.

### <a name="to-regenerate-the-service-registration-key"></a>서비스 등록 키를 다시 생성하려면
1. **StorSimple 디바이스 관리자** 블레이드에서 **관리 &gt;** **키** 로 이동합니다.
    
    ![키 블레이드로 이동](./media/storsimple-8000-manage-service/regenregkey2.png)

2. **키** 블레이드에서 **다시 생성** 을 클릭합니다.

    ![다시 생성 클릭](./media/storsimple-8000-manage-service/regenregkey3.png)
3. **서비스 등록 키 다시 생성** 블레이드에서 키를 다시 생성하는 경우에 필요한 작업을 검토합니다. 이 서비스에 등록된 모든 후속 디바이스는 새 등록 키를 사용합니다. **다시 생성** 을 클릭하여 확인합니다. 등록이 완료된 후에 알림이 표시됩니다.

    ![다시 생성 확인](./media/storsimple-8000-manage-service/regenregkey4.png)

4. 새 서비스 등록 키가 나타납니다.

5. 이 서비스에 새 디바이스 등록을 위해 이 키를 복사하고 저장합니다.



## <a name="change-the-service-data-encryption-key"></a>서비스 데이터 암호화 키 변경
[!INCLUDE [storage-files-migration-generate-key](../../includes/storage-files-migration-generate-key.md)]

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>업데이트 5.0 이전 버전을 실행하는 디바이스에서 지원되는 작업
Azure Portal에서는 업데이트 5.0 이상을 실행하는 StorSimple 디바이스만이 지원됩니다. 이전 버전을 실행하는 디바이스에는 제한된 지원을 제공합니다. Azure Portal로 마이그레이션한 후 다음 테이블을 사용하여 업데이트 5.0 이전 버전을 실행하는 디바이스에서 지원되는 작업을 알아볼 수 있습니다.

| 작업                                                                                                                       | 지원됨      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| 디바이스 등록                                                                                                               | 예            |
| 일반, 네트워크 및 보안과 같은 디바이스 설정 구성                                                                | 예            |
| 업데이트 검사, 다운로드 및 설치                                                                                             | 예            |
| 디바이스 비활성화                                                                                                               | 예            |
| 디바이스 삭제                                                                                                                   | 예            |
| 볼륨 컨테이너 만들기, 수정 및 삭제                                                                                   | 예             |
| 볼륨 만들기, 수정 및 삭제                                                                                             | 예             |
| 백업 정책 만들기, 수정 및 삭제                                                                                      | 예             |
| 수동 백업 수행                                                                                                            | 예             |
| 예약된 백업 수행                                                                                                         | 해당 없음 |
| backupset에서 복원                                                                                                        | 예             |
| 업데이트 3.0 이상을 실행하는 디바이스에 복제 <br> 원본 디바이스는 업데이트 3.0 이전 버전을 실행하고 있습니다.                                | 예            |
| 업데이트 3.0 이전 버전을 실행하는 디바이스에 복제합니다.                                                                          | 예             |
| 원본 디바이스로 장애 조치 <br> (업데이트 3.0 이전 버전을 실행하는 디바이스에서 업데이트 3.0 이후 버전을 실행하는 디바이스로)                                                               | 예            |
| 대상 디바이스로 장애 조치(failover) <br> (업데이트 3.0 이전 소프트웨어 버전을 실행하는 디바이스로)                                                                                   | 예             |
| 경고 지우기                                                                                                                  | 예            |
| 클래식 포털에서 생성된 백업 정책, 백업 카탈로그, 볼륨, 볼륨 컨테이너, 모니터링 차트, 작업 및 경고 보기 | 예            |
| 디바이스 컨트롤러 설정 및 해제                                                                                              | 예            |


## <a name="next-steps"></a>다음 단계
* [StorSimple 배포 프로세스](storsimple-8000-deployment-walkthrough-u2.md)에 대해 자세히 알아봅니다.
* [StorSimple 스토리지 계정 관리](storsimple-8000-manage-storage-accounts.md)에 대해 자세히 알아봅니다.
* [StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스를 관리](storsimple-8000-manager-service-administration.md)하는 방법을 자세히 알아봅니다.
