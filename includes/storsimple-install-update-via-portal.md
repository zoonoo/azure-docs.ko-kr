<properties 
   pageTitle="Azure 관리 포털에서 업데이트 1.2 설치"
   description="관리 포털을 사용하여 StorSimple 8000 시리즈 업데이트 1.2를 설치하는 방법을 설명합니다."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/08/2015"
   ms.author="v-sharos" />

#### 관리 포털에서 업데이트 1.2를 설치하려면

1. StorSimple 서비스 페이지에서 장치를 선택합니다. **장치** > **유지 관리**로 이동합니다.

2. 페이지 아래쪽에서 **업데이트 검색**을 클릭합니다. 사용 가능한 업데이트를 검색하는 작업이 만들어질 수 있습니다. 작업이 성공적으로 완료되면 알림이 표시됩니다.

3. 동일한 페이지의 **소프트웨어 업데이트** 섹션에 사용할 수 있는 새 소프트웨어 업데이트가 보입니다. 장치에 업데이트 1.2를 적용하기 전에 릴리스 정보를 검토하는 것이 좋습니다.

    ![소프트웨어 업데이트 설치](./media/storsimple-install-update-via-portal/InstallUpdate12_11M.png)

4. 페이지 아래쪽에서 **업데이트 설치**를 클릭합니다.

5. 확인하라는 메시지가 표시됩니다. **확인**을 클릭합니다.

6. **업데이트 설치** 대화 상자가 표시됩니다. 장치가 이 대화 상자에 나열된 검사를 충족해야 합니다. 업데이트 전에 이러한 단계가 완료되었습니다. **위의 요구 사항을 이해하고 장치를 업데이트할 준비가 되었습니다**를 선택합니다. 확인 아이콘을 클릭합니다.

    ![확인 메시지](./media/storsimple-install-update-via-portal/InstallUpdate12_2M.png)

7. 이제 일련의 자동 사전 검사가 시작됩니다. 내용은 다음과 같습니다.

	- **컨트롤러 상태 검사** 장치 컨트롤러가 정상이고 온라인에 있는지 모두 확인합니다.
	
	- **하드웨어 구성 요소 상태 검사** StorSimple 장치에서 모든 하드웨어 구성 요소가 정상인지 확인합니다.
	
	- **데이터 0 검사** 데이터 0이 장치에서 사용할 수 있는지 확인합니다. 이 인터페이스를 사용하지 않는 경우 다음을 사용하도록 설정하고 다시 시도해야 합니다.
	
	- **데이터 2 및 데이터 3 검사** 데이터 2와 데이터 3 네트워크 인터페이스를 사용하지 않도록 설정했는지 확인합니다. 이 인터페이스를 사용하는 경우 사용하지 않도록 설정한 다음 장치를 업데이트해야 합니다. 이 검사는 GA 소프트웨어를 실행하는 장치에서 업데이트 하는 경우에 수행됩니다. 0.1, 0.2, 또는 0.3 버전을 실행하는 장치에는 이 확인이 필요하지 않습니다.
	
	- **게이트웨이 검사** 모든 장치에서 업데이트 1 이전 버전을 실행합니다. 이 검사는 업데이트 1 이전 소프트웨어를 실행하는 모든 장치에서 수행되지만 DATA 0 이외의 다른 네트워크 인터페이스에 대해 구성된 게이트웨이가 있는 장치에서는 실패합니다.
 
	위의 사전 업데이트 검사를 모두 성공적으로 완료한 경우에만 업데이트 1.2가 적용됩니다. 진행 중인 업데이트 전 검사 알림이 표시 됩니다.
  
    ![사전 검사 알림](./media/storsimple-install-update-via-portal/InstallUpdate12_3M.png)

    다음은 업그레이드 전 검사에 실패한 예시입니다. 장치 컨트롤러가 정상이고 온라인인지 모두 확인해야 합니다. 하드웨어 구성 요소 상태도 확인해야 합니다. 이 예시에서는 컨트롤러 0과 컨트롤러 1 구성 요소에 주의가 필요합니다. 혼자서 이러한 문제를 해결할 수 없는 경우 Microsoft 지원에 문의해야 할 수 있습니다.

   	 ![사전 검사 실패](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)

	> [AZURE.NOTE]StorSimple 장치에 업데이트 1.2를 적용한 후 DATA 2 및 DATA 3 검사와 게이트웨이 검사는 이후 업데이트에서 더 이상 필요하지 않습니다. 다른 사전 검사는 여전히 필요합니다.


8. 업그레이드 전 검사를 성공적으로 완료한 후 업데이트 작업이 생성됩니다. 업데이트 작업이 성공적으로 만들어지면 알림이 표시됩니다.
 
    ![업데이트 작업 만들기](./media/storsimple-install-update-via-portal/InstallUpdate12_44M.png)

    그런 다음 업데이트가 장치에 적용됩니다.
 
9. 업데이트 작업의 진행률을 모니터링하려면 **작업 보기**를 클릭합니다. **작업** 페이지에서 업데이트 진행률을 볼 수 있습니다.

    ![업데이트 작업 진행률](./media/storsimple-install-update-via-portal/InstallUpdate12_5M.png)

10. 업데이트를 완료하는 데 몇 시간이 걸릴 수 있습니다. 언제든지 작업의 세부 정보를 볼 수 있습니다.

    ![업데이트 작업 세부 정보](./media/storsimple-install-update-via-portal/InstallUpdate12_6M.png)

11. 작업이 완료되면 **유지 관리** 페이지로 이동하여 **소프트웨어 업데이트**로 스크롤합니다.

12. 장치가 **StorSimple 8000 시리즈 업데이트 1.2(6.3.9600.17584)**를 실행하고 있는지 확인합니다. **마지막 업데이트 날짜**도 수정해야 합니다.

    ![유지 관리 페이지](./media/storsimple-install-update-via-portal/InstallUpdate12_10M.png)

13. 이제 유지 관리 모드 업데이트가 제공되는 것을 확인할 수 있습니다. 이러한 업데이트는 작업 중단 업데이트이므로 장치 가동 중지 시간이 발생할 수 있으며, 장치의 Windows PowerShell 인터페이스를 통해서만 적용할 수 있습니다. StorSimple용 Windows PowerShell을 통해 이러한 업데이트를 설치하려면 [유지 관리 모드 업데이트 설치](storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple)의 지침을 따르세요.

> [AZURE.NOTE]특정 인스턴스에서는 유지 관리 모드 업데이트가 장치에 성공적으로 적용된 후 유지 관리 모드 업데이트를 사용할 수 있음을 나타내는 메시지가 최대 24시간 동안 표시될 수 있습니다.

<!---HONumber=Sept15_HO2-->