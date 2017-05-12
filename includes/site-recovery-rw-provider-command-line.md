UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <데이터 전송에 사용할 IP 주소] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

매개 변수:

* /ServerMode: 필수. 구성 및 프로세스 서버를 모두 설치할지 또는 프로세스 서버만 설치할지 여부를 지정합니다. 입력 값: CS, PS.
* InstallLocation: 필수. 구성 요소가 설치되어 있는 폴더입니다.
* /MySQLCredsFilePath. 필수. MySQL 서버 자격 증명이 저장되어 있는 파일 경로입니다. 파일은 다음 형식이어야 합니다.
* [MySQLCredentials]
* MySQLRootPassword = "<Password>"
* MySQLUserPassword = "<Password>"
* /VaultCredsFilePath. 필수. 자격 증명 모음 자격 증명 파일의 위치입니다.
* /EnvType. 필수. 설치 유형입니다. 값: VMware, NonVMware
* /PSIP 및 /CSIP. 필수. 프로세스 서버와 구성 서버의 IP 주소입니다.
* /PassphraseFilePath. 필수. 암호 파일의 위치입니다.
* /ByPassProxy. 선택 사항입니다. 구성 서버가 프록시 없이 Azure에 연결되도록 지정합니다.
* /ProxySettingsFilePath. 선택 사항입니다. 프록시 설정(인증이 필요한 기본 프록시 또는 사용자 지정 프록시)입니다. 파일은 다음 형식이어야 합니다.
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address>"
* ProxyPort = "<Port>"
* ProxyUserName="<User Name>"
* ProxyPassword="<Password>"
* DataTransferSecurePort. 선택 사항입니다. 복제 데이터용 포트 번호입니다.
* SkipSpaceCheck. 선택 사항입니다. 캐시에 대한 공간 확인을 건너뜁니다.
* AcceptThirdpartyEULA. 필수. 타사 EULA를 수락합니다.
* ShowThirdpartyEULA. 필수. 타사 EULA를 표시합니다. 입력으로 제공되는 경우 다른 모든 매개 변수가 무시됩니다.
