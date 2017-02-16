### <a name="prepare-for-push-install-on-windows-machines"></a>Windows 컴퓨터에 강제 설치 준비

1. Windows 컴퓨터와 프로세스 서버 간에 네트워크 연결이 있는지 확인합니다.
2. 프로세스 서버가 컴퓨터에 액세스하는 데 사용할 수 있는 계정을 만듭니다. 계정에 관리자 권한(로컬 또는 도메인)이 있어야 하고, 이 계정은 강제 설치 및 에이전트 업데이트에만 사용됩니다.

   > [!NOTE]
   > 도메인 계정을 사용하지 않는 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정해야 합니다. HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 레지스트리 키 아래에 새로운 **LocalAccountTokenFilterPolicy** DWORD를 추가하여 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정하고 값을 1로 설정합니다. 다음은 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`** 명령 프롬프트에서 동일한 명령을 수행하는 명령입니다.
   >
   >
2. 보호하려는 컴퓨터의 Windows 방화벽에서 **방화벽을 통해 앱 또는 기능 허용**을 선택합니다. **파일 및 프린터 공유**와 **WMI(Windows Management Instrumentation)**를 사용하도록 설정합니다. 도메인에 속하는 컴퓨터의 경우 GPO를 사용하여 방화벽 설정을 구성할 수 있습니다.

   ![방화벽 설정](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

3. CSPSConfigtool에서 만든 계정을 추가합니다.
    - 구성 서버에 로그인 합니다.
    - **cspsconfigtool.exe**를 엽니다. 바탕 화면에서 바로 가기로 사용할 수 있으며, %ProgramData%\home\svsystems\bin 폴더에 있습니다.
    - **계정 관리** 탭에서 **계정 추가**를 클릭합니다.
    - 만든 계정을 추가합니다. 계정을 추가한 후 컴퓨터에 복제를 사용하도록 설정할 때 이 자격 증명을 제공해야 합니다.


<!--HONumber=Jan17_HO3-->


