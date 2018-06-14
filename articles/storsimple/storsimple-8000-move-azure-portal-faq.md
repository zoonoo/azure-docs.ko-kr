---
title: 클래식에서 Azure Portal로 이동 FAQ | Microsoft Docs
description: 클래식에서 Azure Portal로 StorSimple 장치를 이동하는 방법에 대한 자주 묻는 질문과 대답을 제공합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: ac57894e4f180f42f80479d2031f4dd5ddfdb1be
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
ms.locfileid: "27785182"
---
# <a name="move-storsimple-device-manager-service-from-classic-to-azure-portal-frequently-asked-questions-faq"></a>클래식에서 Azure Portal로 StorSimple 장치 관리자 서비스 이동: FAQ(질문과 대답)

## <a name="overview"></a>개요

다음은 Azure 클래식 포털에서 실행되는 StorSimple 장치 관리자 서비스를 Azure Portal로 이동할 때 제기될 수 있는 질문과 대답입니다.

질문과 대답은 다음과 같은 범주로 정렬됩니다.

* StorSimple 장치 관리자 서비스 이동
* 저장소 계정 이동
* Azure Resource Manager 기반 cmdlet 사용
* StorSimple 데이터 관리자 서비스 사용
* 기타

## <a name="moving-storsimple-device-manager-service"></a>StorSimple 장치 관리자 서비스 이동

### <a name="once-i-have-moved-to-azure-portal-can-i-still-create-a-storsimple-manager-service-in-the-classic-portal"></a>Azure Portal로 이동한 후에도 클래식 포털에서 여전히 StorSimple Manager 서비스를 만들 수 있나요?

번호 일단 StorSimple Manager 서비스를 Azure Portal로 마이그레이션하면 클래식 포털에서 새 서비스를 만들 수 없습니다. 또한 [클래식 포털은 2018년 1월 8일부터 사용할 수 없습니다](https://azure.microsoft.com/updates/azure-portal-updates-for-classic-portal-users). 

### <a name="i-have-multiple-storsimple-managers-running-in-the-classic-portal-can-i-choose-which-ones-to-move-to-the-azure-portal"></a>클래식 포털에서 실행되는 여러 StorSimple Manager가 있습니다. Azure Portal로 이동할 항목을 선택할 수 있나요?

번호 Azure Portal로 이동할 StorSimple Manager를 선택할 수 없습니다. 구독의 모든 StorSimple Manager가 이동됩니다.


### <a name="i-initiated-the-migration-of-my-service-to-the-new-azure-portal-should-i-delete-the-storsimple-manager-from-the-classic-portal"></a>새 Azure Portal로의 서비스 마이그레이션을 시작했습니다. 클래식 포털에서 StorSimple Manager를 삭제해야 하나요? 

번호 클래식 포털에서 이동한 서비스는 삭제하지 않도록 합니다. 마이그레이션이 완료될 때까지 기다립니다. 모든 StorSimple Manager가 새 포털로 이동되면 클래식 포털에서 서비스를 삭제할 필요가 없습니다. 언젠가는 클래식 포털을 더 이상 사용할 수 없게 됩니다.

### <a name="can-i-rename-my-storsimple-device-manager-service-in-the-azure-portal"></a>Azure Portal에서 StorSimple 장치 관리자 서비스의 이름을 바꿀 수 있나요?

번호 Azure Portal에서 서비스를 만든 후에는 서비스 이름을 변경할 수 없습니다. 장치, 볼륨, 볼륨 컨테이너 및 백업 정책 같은 기타 엔터티의 경우도 마찬가지입니다.

### <a name="can-i-create-the-80108020-storsimple-cloud-appliances-with-azure-resource-manager-deployment-model"></a>Azure Resource Manager 배포 모델을 사용하여 8010/8020 StorSimple Cloud Appliance를 만들 수 있나요?

예. Azure Resource Manager 배포 모델에서 새 8010/8020 StorSimple Cloud Appliance를 만들 수 있습니다. 이러한 클라우드 어플라이언스용 기본 VM도 Resource Manager 배포 모델에 포함되어 있습니다.

### <a name="when-we-migrate-subscriptions-to-the-azure-portal-will-the-underlying-vms-for-the-storsimple-cloud-appliances-also-migrate-to-azure-resource-management-deployment-model"></a>Azure Portal로 구독을 마이그레이션할 때 StorSimple Cloud Appliance용 기본 VM도 Azure Resource Management 배포 모델로 마이그레이션되나요?

StorSimple 서비스 이동은 StorSimple Cloud Appliance용 VM 관리와는 관련이 없습니다. 현재 클래식 및 Azure Portal 둘 다에서 StorSimple Cloud Appliance용 VM을 완전히 관리할 수 있습니다.

### <a name="can-i-manage-existing-classic-80108020-storsimple-cloud-appliance-from-the-azure-portal"></a>Azure Portal에서 기존의 클래식 8010/8020 StorSimple Cloud Appliance를 관리할 수 있나요?

예. 기존 8010/8020 클라우드 어플라이언스와 연결된 VM을 Azure Portal에서 관리할 수 있습니다.

업데이트 3.0 이상이 실행되는 StorSimple Cloud Appliance 모델 8010/8020을 만든 경우 새 Azure Portal로 이동하는 서비스의 영향을 받지 않습니다. 아무 문제 없이 클라우드 어플라이언스를 완전히 관리할 수 있습니다. 

클래식 포털에서 업데이트 3.0 이전 버전을 실행하는 클라우드 어플라이언스가 있는 경우 제한된 기능만 사용할 수 있습니다. 자세한 내용은 [업데이트 3 이전 버전을 실행하는 장치에서 지원되지 않는 작업 목록](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50)을 참조하세요.

클라우드 어플라이언스를 업데이트할 수 없습니다. 최신 버전의 소프트웨어를 사용하여 새 클라우드 어플라이언스를 만든 다음, 기존 볼륨 컨테이너를 만들어진 새 클라우드 어플라이언스로 장애 조치(Failover)합니다. 자세한 내용은 [클라우드 어플라이언스로 장애 조치(Failover)](storsimple-8000-cloud-appliance-u2.md#fail-over-to-the-cloud-appliance)를 참조하세요.


### <a name="my-storsimple-8000-series-device-is-running-update-20-i-migrated-my-service-to-new-azure-portal-my-device-connected-successfully-but-it-seems-that-i-am-not-able-to-fully-manage-my-device-how-do-i-resolve-this-behavior"></a>StorSimple 8000 시리즈 장치에서 업데이트 2.0을 실행하고 있습니다. 서비스를 새 Azure Portal로 마이그레이션했습니다. 장치는 성공적으로 연결되는데 장치를 완전히 관리할 수 없는 것 같습니다. 이 동작을 해결하려면 어떻게 해야 하나요?

새 Azure Portal은 업데이트 3.0 이상을 실행하는 StorSimple 장치만 지원합니다. 장치에서 업데이트 2.0이 실행되는 경우 이 장치에 대해 제한된 기능만 사용할 수 있습니다. 자세한 내용은 [업데이트 3 이전 버전을 실행하는 장치에서 지원되지 않는 작업 목록](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50)을 참조하세요.

장치를 완전히 관리하려면 장치에 최신 업데이트를 설치합니다. 자세한 내용은 [업데이트 5 설치](storsimple-8000-install-update-5.md)를 참조하세요.

### <a name="i-just-moved-my-storsimple-manager-service-to-the-azure-portal-i-am-seeing-some-alerts-related-to-my-device-is-this-behavior-related-to-the-move"></a>StorSimple Manager 서비스를 Azure Portal로 이동했습니다. 장치와 관련된 경고가 표시됩니다. 이 동작은 서비스 이동과 관련된 것인가요?

번호 이동 자체는 오류나 경고를 발생하지 않습니다. 경고 권장 사항에 따라 경고를 해결하세요.

### <a name="i-am-using-a-storsimple-50007000-series-device-are-these-also-supported-in-the-azure-portal"></a>StorSimple 5000/7000 시리즈 장치를 사용하고 있습니다. Azure Portal에서도 지원되나요?

번호 StorSimple 5000/7000 시리즈 장치는 Azure Portal에서 지원되지 않습니다.

### <a name="i-am-planning-to-migrate-data-from-storsimple-50007000-series-device-to-a-storsimple-8000-series-device-how-does-moving-a-service-to-azure-portal-impact-my-data-migration"></a>StorSimple 5000/7000 시리즈 장치의 데이터를 StorSimple 8000 시리즈 장치로 마이그레이션하려고 합니다. 서비스를 Azure Portal로 이동하면 데이터 마이그레이션에는 어떤 영향을 주나요? 

StorSimple 5000/7000 시리즈 장치의 데이터를 Azure Portal에서 실행되는 StorSimple 8000 시리즈 장치로 마이그레이션할 수 있습니다. 5000/7000 시리즈에서 8000 시리즈로의 데이터 마이그레이션을 허용하려면 [Microsoft 지원 티켓을 기록](storsimple-8000-contact-microsoft-support.md)합니다.


## <a name="moving-subscriptions-storage-accounts-resource-groups"></a>구독, 저장소 계정, 리소스 그룹 이동

### <a name="can-i-move-storsimple-device-manager-from-one-subscription-to-another-subscription-in-the-azure-portal"></a>StorSimple 장치 관리자를 Azure Portal의 구독 간에 이동할 수 있나요?

번호 StorSimple 장치 관리자 서비스를 구독 간에 이동하는 것은 지원되지 않습니다. 다음 단계로 구성된 수동 프로세스를 수행할 수 있습니다.

* StorSimple 장치에서 데이터를 마이그레이션합니다.
* 장치에서 장치 공장 재설정을 수행합니다. 그러면 모든 로컬 데이터가 삭제됩니다.
* 새 구독을 사용하여 StorSimple 장치 관리자 서비스로 장치를 등록합니다.
* 장치에 데이터를 다시 마이그레이션합니다.

### <a name="do-i-have-to-migrate-the-storage-account-to-azure-resource-manager-deployment-model"></a>저장소 계정을 Azure Resource Manager 배포 모델로 마이그레이션해야 하나요?

번호 클래식 저장소 계정은 Azure Portal에서 완벽하게 관리할 수 있습니다. StorSimple 서비스는 Azure Portal로 이동하면 저장소 계정이 계속 이전처럼 작동합니다.

### <a name="what-happens-to-the-storage-account-after-the-service-is-migrated-to-the-azure-portal"></a>서비스를 Azure Portal로 마이그레이션하면 저장소 계정은 어떻게 되나요?

서비스 이동과 저장소 계정 관리는 관련이 없습니다. 클래식 및 Azure Resource Manager 저장소 계정은 Azure Portal 내에서 완벽하게 관리할 수 있습니다. Azure Portal로 서비스를 이동하면 장치는 계속 이 저장소 계정으로 실행되며 데이터에 영향을 미치지 않습니다.

### <a name="can-i-migrate-storsimple-device-manager-from-one-resource-group-to-another"></a>StorSimple 장치 관리자를 리소스 그룹 간에 마이그레이션할 수 있나요?

번호 한 리소스 그룹에서 만든 StorSimple 장치 관리자를 다른 리소스 그룹으로 이동할 수 없습니다.

## <a name="using-azure-resource-manager-based-cmdlets"></a>Azure Resource Manager 기반 cmdlet 사용

### <a name="i-moved-to-the-new-azure-portal-now-my-scripts-based-on-azure-classic-deployment-model-powershell-cmdlets-are-not-working-what-do-i-need-to-do"></a>새로운 Azure Portal로 이동했습니다. 이제 스크립트가 Azure 클래식 배포 모델의 PowerShell cmdlet에 따라 작동하나요? 수행하려면 무엇이 필요한가요?

기존 Azure 클래식 배포 모델의 PowerShell cmdlet은 Azure Portal에서 지원되지 않습니다. Azure Resource Manager를 통해 장치를 관리하기 위해 스크립트를 업데이트합니다. 스크립트를 업데이트하는 방법에 대한 자세한 내용은 [새 Azure Portal용 샘플](https://github.com/anoobbacker/storsimpledevicemgmttools)을 참조하세요.

### <a name="i-just-moved-to-the-azure-portal-and-needed-to-roll-over-the-service-data-encryption-key-where-is-this-option-in-the-azure-portal"></a>방금 Azure Portal로 이동했으며 서비스 데이터 암호화 키를 롤오버해야 합니다. Azure Portal에서 이 옵션은 어디에 있나요?

서비스 데이터 암호화 키를 롤오버하는 옵션은 Azure Portal에 없습니다. Azure Portal에서 이 롤오버를 수행하는 방법에 대한 자세한 내용은 [서비스 데이터 암호화 키 변경](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)을 참조하세요.

### <a name="i-am-using-windows-powershell-for-storsimple-cmdlets-on-the-storsimple-device-to-perform-operations-such-extract-a-support-package-are-these-cmdlets-affected-when-i-move-to-the-new-azure-portal"></a>StorSimple 장치에서 StorSimple용 Windows PowerShell cmdlet을 사용하여 지원 패키지 추출과 같은 작업을 수행하고 있습니다. 이러한 cmdlet은 새 Azure Portal로 이동할 경우 영향을 받나요?

번호 서비스를 새 Azure Portal로 이동할 경우 온-프레미스 StorSimple 장치(자체적으로 이동의 영향을 받지 않음)과 연결된 StorSimple용 Windows PowerShell cmdlet에는 아무 영향도 미치지 않습니다. Azure Portal에도 아무런 문제를 일으키지 않으면서 이러한 cmdlet을 계속 사용하여 지원 패키지를 만들 수 있습니다. Azure 클래식 배포 모델의 PowerShell cmdlet만 이 이동의 영향을 받습니다.

## <a name="moving-storsimple-data-manager-service"></a>StorSimple 데이터 관리자 서비스 이동

### <a name="i-am-using-storsimple-data-manager-service-in-classic-azure-portal-how-should-i-proceed-with-this-move"></a>클래식 Azure Portal에서 StorSimple 데이터 관리자 서비스를 사용하고 있습니다. 이 이동을 어떻게 진행해야 하나요?

StorSimple 데이터 관리자 서비스를 사용 중인 경우 자동으로 Azure Portal로 이동되었습니다.

## <a name="miscellaneous"></a>기타

### <a name="i-am-running-storsimple-snapshot-manager-for-my-8000-series-device-is-there-any-impact-on-storsimple-snapshot-manager-when-i-move-to-azure-portal"></a>8000 시리즈 장치용 StorSimple Snapshot Manager를 실행하고 있습니다. Azure Portal로 이동할 경우 StorSimple Snapshot Manager는 영향을 받나요?

번호 서비스를 Azure Portal로 이동할 때 StorSimple Snapshot Manager는 아무런 영향도 받지 않습니다. 장치 및 StorSimple Snapshot Manager는 계속 이전처럼 작동합니다.

### <a name="can-i-rename-my-storsimple-device-volume-containers-or-volumes"></a>StorSimple 장치, 볼륨 컨테이너 또는 볼륨의 이름을 바꿀 수 있나요?

번호 Azure Portal에서는 장치, 볼륨, 볼륨 컨테이너 또는 백업 정책의 이름을 바꿀 수 없습니다.

## <a name="next-steps"></a>다음 단계

[업데이트 5.0 이전 버전을 실행하는 장치에서 지원되는 작업](storsimple-8000-manage-service.md#supported-operations-on-devices-running-versions-prior-to-update-50)에 대해 자세히 알아보세요.



