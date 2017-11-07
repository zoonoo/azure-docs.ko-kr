## <a name="prepare-your-intel-nuc"></a>Intel NUC 준비

하드웨어 설정을 완료하려면 다음을 수행해야 합니다.

- Intel NUC를 키트에 포함된 전원 공급 장치에 연결합니다.
- 이더넷 케이블을 사용하여 Intel NUC를 네트워크에 연결합니다.

이제 Intel NUC 게이트웨이 장치의 하드웨어 설정을 완료했습니다.

### <a name="sign-in-and-access-the-terminal"></a>터미널 로그인 및 액세스

Intel NUC의 터미널 환경에 액세스하는 두 가지 옵션이 있습니다.

- Intel NUC에 연결된 키보드 및 모니터가 있는 경우 셸에 직접 액세스할 수 있습니다. 기본 자격 증명은 사용자 이름 **root** 및 암호 **root**입니다.

- 데스크톱 컴퓨터에서 SSH를 사용하여 Intel NUC의 셸에 액세스합니다.

#### <a name="sign-in-with-ssh"></a>SSH를 사용하여 로그인

SSH를 사용하여 로그인하려면 Intel NUC의 IP 주소가 필요합니다. Intel NUC에 연결된 키보드 및 모니터가 있는 경우 `ifconfig` 명령을 사용하여 IP 주소를 찾습니다. 또는 라우터에 연결하여 네트워크에서 장치의 주소를 나열합니다.

사용자 이름 **root** 및 암호 **root**로 로그인합니다.

#### <a name="optional-share-a-folder-on-your-intel-nuc"></a>선택 사항: Intel NUC에서 폴더 공유

필요에 따라 다음 데스크톱 환경을 사용하여 Intel NUC의 폴더를 공유할 수 있습니다. 폴더를 공유하면 `nano` 또는 `vi`를 사용하는 대신 원하는 데스크톱 텍스트 편집기(예: [Visual Studio Code](https://code.visualstudio.com/) 또는 [Sublime Text](http://www.sublimetext.com/))를 사용하여 Intel NUC의 파일을 편집할 수 있습니다.

Windows 폴더를 공유하려면 Intel NUC에서 Samba 서버를 구성합니다. 또는 데스크톱 컴퓨터에서 SFTP 클라이언트를 통해 Intel NUC의 SFTP 서버를 사용합니다.
