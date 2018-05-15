| **구성 요소** | **요구 사항** |
| --- |---|
| CPU 코어| 8 |
| RAM | 16GB|
| 디스크 수 | 3, OS 디스크, 프로세스 서버 캐시 디스크, 보존 드라이브(장애 복구용) 포함 |
| 사용 가능한 디스크 공간(프로세스 서버 캐시) | 600GB
| 사용 가능한 디스크 공간(보존 디스크) | 600GB|
| 운영 체제  | Windows Server 2012 R2 <br> Windows Server 2016 |
| 운영 체제 로케일 | 미국 영어(en-us)|
| VMware vSphere PowerCLI 버전 | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server 역할 | 다음 역할을 사용하지 않도록 설정함: <br> - Active Directory Domain Services <br>- 인터넷 정보 서비스 <br> - Hyper-V |
| 그룹 정책| 다음 그룹 정책을 사용하지 않도록 설정함: <br> - 명령 프롬프트에 대한 액세스 방지 <br> - 레지스트리 편집 도구에 대한 액세스 방지 <br> - 파일 첨부를 위한 트러스트 논리 <br> - 스크립트 실행 켜기 <br> [자세히 알아보기](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | - 기존의 기본 웹 사이트 없음 <br> - [익명 인증](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) 사용 <br> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 설정 사용  <br> - 포트 443에서 수신 대기하는 기존의 웹 사이트/응용 프로그램 없음<br>|
| NIC 유형 | VMXNET3(VMware VM으로 배포될 경우) |
| IP 주소 유형 | 공용 |
| 인터넷 액세스 | 서버에서 다음 URL에 액세스해야 합니다. <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi(스케일 아웃 서버에는 필요하지 않음) <br> - time.nist.gov <br> - time.windows.com <br> \*.visualstudio.com |
| 포트 | 443(컨트롤 채널 오케스트레이션)<br>9443(데이터 전송)|
