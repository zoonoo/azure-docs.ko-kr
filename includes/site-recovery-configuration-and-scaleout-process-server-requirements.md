| **하드웨어** | |
| --- |---|
| CPU 코어 수| 8 |
| RAM| 12GB|
| 디스크 수 | 3 <br><br> - OS 디스크<br> - 프로세스 서버 캐시 디스크<br> - 보존 드라이브(장애 복구용)|
| 사용 가능한 디스크 공간(프로세스 서버 캐시) | 600GB
| 사용 가능한 디스크 공간(보존 디스크) | 600GB|
| **소프트웨어** | |
| 운영 체제 버전 | Windows Server 2012 R2 |
| 운영 체제 로케일 | 미국 영어(en-us)|
| VMware vSphere PowerCLI 버전 | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server 역할 | 다음 역할은 사용하지 마세요. <br> - Active Directory Domain Services <br>- 인터넷 정보 서비스 <br> - Hyper-V |
| 그룹 정책| 다음 그룹 정책은 서버에서 사용하도록 설정하지 않아야 합니다. <br> - 명령 프롬프트에 대한 액세스 방지 <br> - 레지스트리 편집 도구에 대한 액세스 방지 <br> - 파일 첨부를 위한 트러스트 논리 <br> - 스크립트 실행 켜기 <br> **참고:** 이러한 그룹 정책에 대한 자세한 내용은 [여기](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx)를 참조하세요.|
| IIS(인터넷 정보 서비스) 구성 | - 기존의 기본 웹 사이트 없음 <br> - [익명 인증](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx) 사용 <br> - [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx) 설정 사용  <br> - 포트 443에서 수신 대기하는 기존 웹 사이트/응용 프로그램 없음<br>|
| **네트워크** | |
| 네트워크 인터페이스 카드 유형 | VMXNET3 |
| IP 주소 유형 | 정적 |
| 인터넷 액세스 | 서버는 직접 또는 프록시 서버를 통해 다음 URL에 액세스할 수 있어야 합니다. <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi(확장 프로세스 서버에는 필요하지 않음) <br> - time.nist.gov <br> - time.windows.com |
| 포트 | 443(컨트롤 채널 오케스트레이션)<br>9443(데이터 전송)|
