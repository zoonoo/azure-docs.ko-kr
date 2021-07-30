---
title: StorSimple 8000 시리즈 디바이스에 업데이트 5.1 설치 | Microsoft Docs
description: StorSimple 8000 시리즈 디바이스에 StorSimple 8000 시리즈 업데이트 5.1을 설치하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/21/2021
ms.author: alkohli
ms.openlocfilehash: 289ffbbd3dc23be0060ee5dbe488bf80323214e2
ms.sourcegitcommit: 19dcad80aa7df4d288d40dc28cb0a5157b401ac4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107897220"
---
# <a name="install-update-51-on-your-storsimple-device"></a>StorSimple 디바이스에 업데이트 5.1 설치

## <a name="overview"></a>개요

이 자습서에서는 Azure Portal 또는 핫픽스 방법을 통해 이전 소프트웨어 버전을 실행하는 StorSimple 디바이스에 업데이트 5.1을 설치하는 방법을 설명합니다.

업데이트 5.1에는 Azure Portal 또는 핫픽스 방법을 통해 적용될 수 있는 무중단 보안 업데이트가 포함되어 있습니다.

Azure Portal에서 업데이트 5.1을 적용하는 경우 하드웨어 상태 및 네트워크 연결 측면에서 디바이스 상태를 확인하기 위해 수동 및 자동 사전 검사가 수행됩니다. 이러한 사전 검사는 Azure Portal에서 업데이트를 적용할 때만 발생합니다.

핫픽스 방법을 사용할 경우에는 [StorSimple 디바이스에 업데이트 5 설치](storsimple-8000-install-update-5.md) 안내에 따라 업데이트 5를 먼저 설치하는 것이 좋습니다. 그런 다음, 아래에서 [핫픽스로 업데이트 5.1 설치](#install-update-51-as-a-hotfix)의 단계에 따라 업데이트 5.1을 설치합니다.

업데이트 5.1의 보안 업데이트는 설치하는 데 약 30분 정도 걸립니다.

> [!IMPORTANT]
> * 업데이트 5.1은 필수 업데이트로, 즉시 설치해야 합니다. 자세한 내용은 [업데이트 5.1 릴리스 정보](storsimple-update51-release-notes.md)를 참조하세요.
> * 업데이트 5는 지원되는 최소 버전입니다.

> [!NOTE]
> * Azure Portal을 통해 소프트웨어 및 기타 정기적인 업데이트를 설치하는 것이 좋습니다. <!--You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device. - N/A FOR 5.1? No maintenance mode updates, and the security updates go quickly.-->
> * 핫픽스 방법을 사용하여 설치하려는 경우 설치를 시작하기 전에 [Microsoft 지원](mailto:support@microsoft.com)에 연락해야 합니다.

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>Azure Portal을 통해 업데이트 5.1 설치

디바이스를 [업데이트 5.1](storsimple-update51-release-notes.md)로 업데이트하려면 다음 단계를 수행합니다.

> [!NOTE]
> Microsoft는 디바이스로부터 추가적인 진단 정보를 확보할 수 있습니다. 따라서 운영 팀에서 문제가 있는 디바이스를 확인하는 경우, 디바이스로부터 정보를 수집하고 문제를 진단할 준비가 더욱 잘 갖추어져 있습니다.

#### <a name="to-install-an-update-from-the-azure-portal"></a>Azure 포털에서 업데이트를 설치하려면

1. StorSimple 서비스 페이지에서 디바이스를 선택합니다.

    ![디바이스 선택](./media/storsimple-8000-install-update-51/update1.png)

2. **디바이스 설정** > **디바이스 업데이트** 로 이동합니다.

    ![디바이스 업데이트 옵션이 설명선으로 표시된 설정 블레이드 스크린샷](./media/storsimple-8000-install-update-51/update2.png)

3. 새 업데이트를 사용할 수 있는 경우 알림이 나타납니다. 또는 **디바이스 업데이트** 블레이드에서 **업데이트 검색** 을 클릭합니다. 사용 가능한 업데이트를 검색하는 작업이 만들어집니다. 작업이 성공적으로 완료되면 알림이 표시됩니다.

    ![디바이스 업데이트 옵션이 설명선으로 표시된 설정 블레이드 및 새 정기 업데이트를 사용할 수 있다는 메시지가 설명선으로 표시된 디바이스 업데이트 블레이드 스크린샷](./media/storsimple-8000-install-update-51/update3.png)

4. 디바이스에 업데이트를 적용하기 전에 릴리스 정보를 검토하는 것이 좋습니다. 업데이트를 적용하려면 **업데이트 설치** 를 클릭합니다. **Confirm regular updates(정기 업데이트 확인)** 블레이드에서 업데이트를 적용하기 전에 완료할 필수 구성 요소를 검토합니다. 디바이스를 업데이트할 준비가 되었음을 나타내는 확인란을 선택한 후 **설치** 를 클릭합니다.

    ![업데이트 설치 옵션이 설명선으로 표시된 디바이스 업데이트 블레이드 및 동의함 옵션과 설치 옵션이 설명선으로 표시된 정기 업데이트 확인 스크린샷](./media/storsimple-8000-install-update-51/update4.png)

5. 일련의 필수 조건 검사가 시작됩니다. 이들 검사는 다음과 같습니다.
   
   * **컨트롤러 상태 검사** 디바이스 컨트롤러가 정상이고 온라인에 있는지 모두 확인합니다.
   * **하드웨어 구성 요소 상태 검사** StorSimple 디바이스에서 모든 하드웨어 구성 요소가 정상인지 확인합니다.
   * **데이터 0 검사** 데이터 0이 디바이스에서 사용할 수 있는지 확인합니다. 이 인터페이스를 사용하지 않는 경우 다음을 사용하도록 설정하고 다시 시도해야 합니다.

     모든 검사가 성공적으로 완료된 경우에만 업데이트가 다운로드되어 설치됩니다. 검사가 진행 중인 경우 알림이 표시됩니다. 사전 검사가 실패하면 실패한 이유가 표시됩니다. 이러한 문제를 해결한 다음 작업을 다시 시도합니다. 혼자서 이러한 문제를 해결할 수 없는 경우 Microsoft 지원에 문의해야 할 수 있습니다.

6. 사전 검사를 성공적으로 완료한 후 업데이트 작업이 생성됩니다. 업데이트 작업이 성공적으로 만들어지면 알림이 표시됩니다.
   
    ![“소프트웨어 업데이트 작업 시작”이라는 알림 스크린샷](./media/storsimple-8000-install-update-51/update6.png)
   
    그런 다음 업데이트가 디바이스에 적용됩니다.

7. 업데이트를 완료하는 데 몇 시간이 걸립니다. 업데이트 작업을 선택하고 **세부 정보** 를 클릭하여 언제든지 작업의 세부 정보를 봅니다.

    ![소프트웨어 업데이트의 다운로드 및 설치가 진행 중입니다. 옵션이 설명선으로 표시된 디바이스 업데이트 블레이드 스크린샷](./media/storsimple-8000-install-update-51/update8.png)

     **디바이스 설정 &gt; 작업** 에서 업데이트 작업의 진행률을 모니터링할 수도 있습니다. **작업** 블레이드에서 업데이트 진행률을 볼 수 있습니다.

     ![작업이 설명선으로 표시된 설정 블레이드 및 업데이트 진행률이 표시된 작업 블레이드 스크린샷](./media/storsimple-8000-install-update-51/update7.png)

8. 작업이 완료되면 **디바이스 설정 &gt; 디바이스 업데이트** 로 이동합니다. 이제 소프트웨어 버전이 업데이트됩니다.

   디바이스에서 **StorSimple 8000 시리즈 업데이트 5.1(6.3.9600.17885)** 을 실행 중이고 **마지막 업데이트 날짜** 가 오늘 날짜인지 확인합니다.

## <a name="install-update-51-as-a-hotfix"></a>핫픽스로 업데이트 5.1 설치

업데이트 5.1을 핫픽스로 설치하려면 설치를 시작하기 전에 다음 단계를 수행합니다.

* 업데이트 5.1을 설치하기 전에 업데이트 5를 설치합니다. 자세한 내용은 [StorSimple 디바이스에 업데이트 5 설치](storsimple-8000-install-update-5.md)를 참조하세요.
* 핫픽스 설치를 시작하기 전에 [Microsoft 지원](mailto:support@microsoft.com)에 연락하세요.

핫픽스 메서드에는 다음 단계가 포함됩니다.

1. Microsoft Update 카탈로그에서 핫픽스를 다운로드합니다.
2. 일반 모드 핫픽스를 설치 및 확인합니다.
3. 유지 관리 모드 핫픽스를 설치 및 확인합니다.

#### <a name="download-updates-for-your-device"></a>디바이스에 대한 업데이트 다운로드

다음 핫픽스를 기술된 순서에 따라 제안된 폴더에 다운로드하고 설치해야 합니다.

| 주문 | KB       | 설명 | 업데이트 유형 | 설치 시간 |폴더에 설치|
|-------|----------|------------ |-------------|--------------|----- |
|1.     |KB4542887|소프트웨어 업데이트<br>_HcsSoftwareUpdate.exe_ 및 _CisMSDAgent.exe_ 를 둘 다 다운로드 |Regular <br></br>중단 없음 |~ 25분 |FirstOrderUpdate|
|3.<sup>1, 2</sup>     |KB4037263|디스크 펌웨어|유지 관리 <br></br>중단|~ 30분|ThirdOrderUpdate|

<sup>1</sup> 업데이트 5.1에서는 두 번째 순서 업데이트가 없습니다.

<sup>2</sup> 업데이트 5의 핫픽스 업데이트 위에 디스크 펌웨어 업데이트를 설치하지 않은 경우 세 번째 순서 업데이트를 설치합니다.

다음 단계에 따라 핫픽스를 다운로드하고 설치합니다.

#### <a name="download-hotfixes"></a>핫픽스 다운로드

핫픽스를 다운로드하려면 [핫픽스 다운로드](storsimple-8000-install-update-5.md#to-download-hotfixes)를 참조하세요.

#### <a name="install-and-verify-device-updates"></a>디바이스 업데이트 설치 및 확인

**StorSimple 디바이스에 업데이트 5 설치** 에서 [일반 모드 핫픽스 설치 및 확인](storsimple-8000-install-update-5.md#to-install-and-verify-regular-mode-hotfixes)의 단계에 따라 KB4542887의 디바이스 업데이트를 설치합니다.

> [!IMPORTANT]
> 아직 [Microsoft 지원](mailto:support@microsoft.com)에 연락하지 않았으면 핫픽스를 설치하기 전 지금 연락해야 합니다.

단계에 따라 첫 번째 순서 업데이트를 설치합니다. 업데이트 5.1에는 두 번째 순서 업데이트가 없습니다.

업데이트 5.1의 경우 설치 후 다음 소프트웨어 버전을 확인합니다.

 * FriendlySoftwareVersion: StorSimple 8000 시리즈 업데이트 5.1
 * HcsSoftwareVersion: 6.3.9600.17885
 * CisAgentVersion: 1.0.9777.0
 * MdsAgentVersion: 35.2.2.0
 * Lsisas2Version: 2.0.78.00

#### <a name="install-and-verify-disk-firmware-updates"></a>디스크 펌웨어 업데이트 설치 및 확인

업데이트 5를 설치할 때 디스크 펌웨어 업데이트를 설치하지 않았으면 **StorSimple 디바이스에 업데이트 5 설치** 에서 [일반 모드 핫픽스 설치 및 확인](storsimple-8000-install-update-5.md#to-install-and-verify-maintenance-mode-hotfixes)의 단계에 따라 KB4037263의 디스크 펌웨어 업데이트를 설치합니다.

`XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107` 펌웨어 버전을 실행하는 경우 디스크 펌웨어 업데이트를 설치할 필요가 없습니다.

디스크 펌웨어 업데이트가 필요한지 확인하려면 `Get-HcsFirmwareVersion` cmdlet을 실행합니다.

## <a name="next-steps"></a>다음 단계

[업데이트 5.1 릴리스](storsimple-update51-release-notes.md)에 대해 자세히 알아봅니다.
