<!--author=alkohli last changed: 03/17/16-->

## 업데이트 오류 문제 해결
**업그레이드 전 검사에 실패했다는 알림을 보는 경우 어떻게 되나요?**

사전 검사가 실패하면 페이지의 맨 아래에 있는 자세한 알림 표시줄에 있는지 확인합니다. 이 사전 검사 실패와 관련된 지침을 제공합니다. 다음 그림에서는 이러한 알림이 표시되는 인스턴스를 보여줍니다. 이 경우에 컨트롤러 상태 확인 및 하드웨어 구성 요소 상태 검사에 실패했습니다. **하드웨어 상태** 섹션에서, **컨트롤러 0**과 **컨트롤러 1** 구성 요소 모두에 주의가 필요한 지 볼 수 있습니다.

  ![사전 검사 실패](./media/storsimple-install-troubleshooting/HCS_PreUpdateCheckFailed-include.png)

두 컨트롤러가 정상이고 온라인 상태인지 확인해야 합니다. 유지 관리 페이지에서 StorSimple 장치에서 모든 하드웨어 구성 요소가 정상으로 표시되어 있는지도 확인해야 합니다. 그런 다음 업데이트를 설치할 수 있습니다. 하드웨어 구성 요소 문제를 해결할 수 없는 경우, 다음 단계는 Microsoft 지원에 문의해야 합니다.

**"업데이트를 설치하지 못했습니다" 오류 메시지를 수신하고 업데이트 문제 해결 가이드를 참조하여 실패의 원인을 확인하는 것이 좋은 경우 어떻게 되나요?**

가능한 원인 중 하나는 Microsoft 업데이트 서버에 연결되지 않은 것일 수 있습니다. 수행해야 하는 수동 검사입니다. 업데이트 서버에 대한 연결이 손실된 경우 업데이트 작업이 실패합니다. StorSimple 장치의 Windows PowerShell 인터페이스에서 다음 cmdlet을 실행하여 연결을 확인할 수 있습니다.

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

두 컨트롤러에서 cmdlet을 실행합니다.

연결되었음을 확인했으나 계속 이 문제가 발생하는 경우, 다음 단계는 Microsoft 지원에 문의하세요.

<!---HONumber=AcomDC_0323_2016-->