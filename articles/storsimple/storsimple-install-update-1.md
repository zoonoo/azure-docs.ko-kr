---
title: "StorSimple 장치에 업데이트 1.2 설치 | Microsoft Docs"
description: "StorSimple 8000 시리즈 장치에서 StorSimple 8000 시리즈 업데이트 1.2를 설치하는 방법에 대해 설명합니다."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 7a513923-eb77-4078-b0ab-f8e90183796a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 56af9046fff24229298997b169aaf329e1692cc2
ms.openlocfilehash: 80ff35cc47dfc38089f4c392ef4c90baf9ccc03e
ms.lasthandoff: 02/28/2017


---
# <a name="install-update-12-on-your-storsimple-8000-series-device"></a>StorSimple 8000 시리즈 장치에 업데이트 1.2 설치
## <a name="overview"></a>개요
이 자습서에서는 업데이트 1 이전 소프트웨어 버전을 실행하는 StorSimple 장치에 업데이트 1.2를 설치하는 방법에 대해 설명합니다. 또한 이 자습서는 StorSimple 장치의 DATA 0 이외의 다른 네트워크 인터페이스에서 게이트웨이를 구성하는 경우 업데이트에 필요한 추가 단계를 다룹니다.

업데이트 1.2는 장치 소프트웨어 업데이트, LSI 드라이버 업데이트 및 디스크 펌웨어 업데이트를 포함합니다. 소프트웨어 및 LSI 드라이버 업데이트는 무중단 업데이트이며 Azure 클래식 포털을 통해 적용될 수 있습니다. 디스크 펌웨어 업데이트는 작업 중단 업데이트이며 장치의 Windows PowerShell 인터페이스를 통해서만 적용할 수 있습니다.

장치가 실행되는 버전에 따라 업데이트 1.2를 적용할지 결정할 수 있습니다. 장치 **대시보드**의 **빠른 보기** 섹션으로 이동하여 장치의 소프트웨어 버전을 확인할 수 있습니다.

</br>

| 소프트웨어 버전을 실행하는 경우... | 포털에서 어떻게 될까요? |
| --- | --- |
| 릴리스 - GA |릴리스 버전(GA)을 실행하는 경우 이 업데이트를 적용하지 않습니다. [Microsoft 지원에 문의](storsimple-contact-microsoft-support.md) 하여 장치를 업데이트합니다. |
| 업데이트 0.1 |포털은 업데이트 1.2를 적용합니다. |
| 업데이트 0.2 |포털은 업데이트 1.2를 적용합니다. |
| 업데이트 0.3 |포털은 업데이트 1.2를 적용합니다. |
| 업데이트 1 |이 업데이트는 사용할 수 없습니다. |
| 업데이트 1.1 |이 업데이트는 사용할 수 없습니다. |

</br>

> [!IMPORTANT]
> * 업데이트의 단계적 롤아웃을 수행하기 때문에 즉시 업데이트 1.2를 볼 수는 없습니다. 해당 업데이트를 곧 사용할 수 있게 되므로 몇 일 후에 업데이트를 스캔합니다.
> * 이 업데이트는 일련의 수동 및 자동 전 검사를 포함하여 하드웨어 상태 및 네트워크 연결 측면에서 장치 상태를 확인합니다. Azure 클래식 포털에서 업데이트를 적용하는 경우 이러한 사전 검사를 수행합니다.
> * Azure 클래식 포털을 통해 소프트웨어 및 드라이버 업데이트를 설치하는 것이 좋습니다. 포털에서 사전 업데이트 게이트웨이 검사가 실패한 경우 (업데이트를 설치하려면) 장치의 Windows PowerShell 인터페이스로 이동해야 합니다. 업데이트를 설치하는 데 5-10시간이 걸릴 수 있습니다.(Windows 업데이트 포함) 장치의 Windows PowerShell 인터페이스를 통해 유지 관리 모드 업데이트를 설치해야 합니다. 유지 관리 모드 업데이트는 중단 업데이트입니다. 이는 장치에 중단 시간을 발생시킵니다.
> 
> 

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-12-via-the-azure-classic-portal"></a>Azure 클래식 포털을 사용하여 업데이트 1.2 설치
다음 단계를 수행하여 장치를 [업데이트 1.2](storsimple-update1-release-notes.md)로 업데이트합니다. 장치에서 데이터 0 네트워크 인터페이스에 구성된 게이트웨이가 있는 경우 이 절차를 사용합니다.

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

1. 장치가 **StorSimple 8000 시리즈 업데이트 1.2(6.3.9600.17584)**를 실행하고 있는지 확인합니다. **마지막 업데이트 날짜** 도 수정해야 합니다. 또한 유지 관리 모드 업데이트가 사용 가능하다고 표시됩니다(이 메시지는 업데이트를 설치한 후 최대 24시간 동안 계속 표시될 수 있음).
   
   유지 관리 모드 업데이트는 작업 중단 업데이트이므로 장치 가동 중지 시간이 발생할 수 있으며, 장치의 Windows PowerShell 인터페이스를 통해서만 적용할 수 있습니다.
   
   ![유지 관리 페이지](./media/storsimple-install-update-1/InstallUpdate12_10M.png "유지 관리 페이지")
2. [핫픽스를 다운로드하려면](#to-download-hotfixes)에 나열된 단계를 사용하여 유지 관리 모드 업데이트를 다운로드한 후 KB3063416을 검색한 후 다운로드합니다. 이 KB는 디스크 펌웨어 업데이트를 설치합니다(다른 업데이트가 이미 설치되어 있어야 함).
3. [유지 관리 모드 핫픽스 설치 및 확인](#to-install-and-verify-maintenance-mode-hotfixes) 에 나열된 단계를 따라 유지 관리 모드 업데이트를 설치합니다.
4. Azure 클래식 포털에서 **유지 관리** 페이지로 이동한 후 페이지 아래쪽에서 **업데이트 검색**을 클릭하여 Windows 업데이트를 확인한 다음 **업데이트 설치**를 클릭합니다. 모든 업데이트가 정상적으로 설치되면 완료됩니다.

## <a name="install-update-12-on-a-device-that-has-a-gateway-configured-for-a-non-data-0-network-interface"></a>비데이터 0 네트워크 인터페이스에 구성된 게이트웨이가 있는 장치에 업데이트 1.2 설치
Azure 클래식 포털을 통해 업데이트를 설치하려고 할 때 게이트웨이 검사에 실패하는 경우 이 절차를 사용해야 합니다. 비데이터 0 네트워크 인터페이스에 할당된 게이트웨이가 있고 장치가 업데이트 1 이전의 소프트웨어 버전을 실행하는 경우 확인이 실패합니다. 장치에 비데이터 0 네트워크 인터페이스에 대한 게이트웨이가 없는 경우, Azure 클래식 포털에서 직접 장치를 업데이트할 수 있습니다. [Azure 클래식 포털을 사용하여 업데이트 1.2 설치](#install-update-1.2-via-the-azure-classic-portal)를 참조하세요.

이 메서드를 사용하여 업그레이드할 수 있는 소프트웨어 버전 업데이트 0.1, 0.2 및 0.3 도 업데이트됩니다.

> [!IMPORTANT]
> * 장치가 릴리스(GA) 버전을 실행하는 경우 이 업데이트 설치를 위한 지원을 받으려면 [Microsoft 지원에 문의](storsimple-contact-microsoft-support.md) 하세요.
> * 이 절차는 업데이트 1.2에만 적용하도록 한 번만 수행해야 됩니다. Azure 클래식 포털을 사용하여 후속 업데이트를 적용할 수 있습니다.
> 
> 

장치가 업데이트 1 이전 소프트웨어를 실행하고 데이터 0 이외의 다른 네트워크 인터페이스에 대해 게이트웨이가 설정된 경우, 다음 두 가지 방법으로 업데이트 1.2를 적용할 수 있습니다.

* **옵션 1**: 업데이트를 다운로드하고 장치의 Windows PowerShell 인터페이스에서 `Start-HcsHotfix` cmdlet를 사용하여 적용합니다. 이것이 권장된 방법입니다. **장치가 업데이트 1.0 또는 1.1 업데이트를 실행 중인 경우 이 메서드를 사용하여 업데이트 1.2를 적용하지 마세요.**
* **옵션 2**: 게이트웨이 구성을 제거하고 Azure 클래식 포털에서 직접 업데이트를 설치합니다.

각각에 대한 자세한 지침은 다음 섹션에 제공됩니다.

## <a name="option-1-use-windows-powershell-for-storsimple-to-apply-update-12-as-a-hotfix"></a>옵션 1: StorSimple용 Windows PowerShell을 사용하여 업데이트 1.2를 핫픽스로 적용
업데이트 0.1, 0.2, 0.3을 실행하고 Azure 클래식 포털에서 업데이트를 설치할 때 게이트웨이 검사에 실패한 경우 이 절차를 사용해야 합니다. 릴리스(GA) 소프트웨어를 실행하는 경우 [Microsoft 지원](storsimple-contact-microsoft-support.md) 에 문의하여 장치를 업데이트합니다.

업데이트 1.2를 핫픽스로 설치하려면 다음 핫픽스를 다운로드한 후 설치해야 합니다.

| 순서 | KB | 설명 | 업데이트 유형 |
| --- | --- | --- | --- |
| 1 |KB3063418 |소프트웨어 업데이트 |일반 |
| 2 |KB3043005 |LSI SAS 컨트롤러 업데이트 |일반 |
| 3 |KB3063416 |디스크 펌웨어 |유지 관리 |

이 절차를 사용하기 전에 업데이트를 적용하려면 다음을 확인하세요.

* 두 장치 컨트롤러가 온라인 상태가 됩니다.

다음 단계를 수행하여 업데이트 1.2를 적용합니다. **업데이트를 완료하는 데 2시간 정도가 걸릴 수 있습니다.(소프트웨어에 약 30분, 드라이버에 30분, 디스크 펌웨어에 45분)**

[!INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]

## <a name="option-2-use-the-azure-classic-portal-to-apply-update-12-after-removing-the-gateway-configuration"></a>옵션 2: Azure 클래식 포털을 사용하여 게이트웨이 구성을 제거한 후에 업데이트 1.2를 적용하려면
이 절차는 업데이트 1 이전의 소프트웨어 버전을 실행 중이고 데이터 0 이외의 다른 네트워크 인터페이스에 게이트웨이가 설정된 StorSimple 장치에 적용됩니다. 업데이트를 적용하기 전에 게이트웨이 설정의 선택을 취소해야 합니다.

업데이트를 완료하는데 몇 시간이 걸릴 수 있습니다. 호스트가 서로 다른 서브넷에 있는 경우 iSCSI 인터페이스에서 게이트웨이 구성을 제거하면 가동 중지 시간이 발생할 수 있습니다. 작동 중지 시간을 줄이려면 iSCSI 트래픽에 대해 데이터 0을 구성하는 것이 좋습니다.

다음 단계를 수행하여 게이트웨이로 네트워크 인터페이스를 사용하지 않도록 설정하고 업데이트를 적용합니다.

[!INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>다음 단계
[업데이트 1.2 릴리스](storsimple-update1-release-notes.md)에 대해 자세히 알아봅니다.


