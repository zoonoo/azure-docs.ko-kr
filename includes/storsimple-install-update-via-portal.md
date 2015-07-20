
#### 관리 포털에서 업데이트 1을 설치하려면

1. StorSimple 서비스 페이지에서 장치를 선택합니다. **장치** > **유지 관리**로 이동합니다.

2. 페이지 아래쪽에서 **업데이트 검색**을 클릭합니다. 사용 가능한 업데이트를 검색하는 작업이 만들어질 수 있습니다. 작업이 성공적으로 완료되면 알림이 표시됩니다.

3. 동일한 페이지의 **소프트웨어 업데이트** 섹션에 사용할 수 있는 새 소프트웨어 업데이트가 보입니다. 장치에서 Update 1.0을 적용하기 전에 릴리스 정보를 검토하는 것이 좋습니다.

    ![소프트웨어 업데이트 설치](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates1-include.png)

4. 페이지 아래쪽에서 **업데이트 설치**를 클릭합니다.

5. 확인하라는 메시지가 표시됩니다. **확인**을 클릭합니다.

6. **업데이트 설치** 대화 상자가 표시됩니다. 장치가 이 대화 상자에 나열된 검사 항목을 만족하는지 확인합니다. **위의 요구 사항을 이해하고 장치를 업데이트할 준비가 되었습니다**를 선택합니다. 확인 아이콘을 클릭합니다.

    ![확인 메시지](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates2-include.png)

7. 진행 중인 업데이트 전 검사 알림이 표시 됩니다.
  
    ![사전 검사 알림](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates3-include.png)

    다음은 업그레이드 전 검사에 실패한 예시입니다. 장치 컨트롤러가 정상이고 온라인인지 모두 확인해야 합니다. 하드웨어 구성 요소 상태도 확인해야 합니다. 이 예시에서는 컨트롤러 0과 컨트롤러 1 구성 요소에 주의가 필요합니다. 혼자서 이러한 문제를 해결할 수 없는 경우 Microsoft 지원에 문의해야 할 수 있습니다.

    ![사전 검사 실패](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)

8. 업그레이드 전 검사를 성공적으로 완료한 후 업데이트 작업이 생성됩니다. 업데이트 작업이 성공적으로 만들어지면 알림이 표시됩니다.
 
    ![업데이트 작업 만들기](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates4-include.png)

    그런 다음 업데이트가 장치에 적용됩니다.
 
9. 업데이트 작업의 진행률을 모니터링하려면 **작업 보기**를 클릭합니다. 작업 페이지에서 업데이트 진행률을 볼 수 있습니다.

    ![업데이트 작업 진행률](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates5-include.png)

10. 업데이트를 완료하는 데 몇 시간이 걸릴 수 있습니다. 언제든지 작업의 세부 정보를 볼 수 있습니다.

    ![업데이트 작업 세부 정보](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates6-include.png)

11. 작업이 완료되면 **유지 관리** 페이지로 이동하여 **소프트웨어 업데이트**로 스크롤합니다.

12. 장치가 **StorSimple 8000 시리즈 업데이트 1.0(6.3.9600.17491)**를 실행하고 있는지 확인합니다. **마지막 업데이트 날짜**도 수정해야 합니다.

    ![유지 관리 페이지](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates7-include.png)

<!---HONumber=July15_HO2-->