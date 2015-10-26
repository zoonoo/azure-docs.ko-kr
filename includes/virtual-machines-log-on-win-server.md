<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="cynthn" solutions="" manager="timlt" editor="tysonn" />

4. **연결**을 클릭하면 원격 데스크톱 프로토콜 파일(.rdp 파일)이 생성되어 다운로드됩니다. **열기**를 클릭하여 이 파일을 사용합니다.

5. 원격 데스크톱 창에서 **연결**을 클릭하여 계속합니다.

	![연결 계속](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Windows 보안 창에서 가상 컴퓨터의 계정에 대한 자격 증명을 입력하고 **확인**을 클릭합니다.

 	대부분의 경우 가상 컴퓨터를 만들 때 지정한 로컬 계정 사용자 이름과 암호를 입력합니다. 이 경우 도메인은 가상 컴퓨터의 이름이므로 *vmname*&#92;*username*으로 입력합니다.
	
	가상 컴퓨터가 조직의 도메인에 속하는 경우에는 사용자 이름에 해당 도메인의 이름을 *Domain*&#92;*Username* 형식으로 포함해야 합니다. 계정은 관리자 그룹에 있거나 VM에 대한 원격 액세스 권한을 부여받아야 합니다.
	
	가상 컴퓨터가 도메인 컨트롤러인 경우 해당 도메인에 대한 도메인 관리자 계정의 사용자 이름과 암호를 입력합니다.

7.	**예**를 클릭하여 가상 컴퓨터의 ID를 확인하고 로그온을 완료합니다.

	![컴퓨터의 ID 확인](./media/virtual-machines-log-on-win-server/connectverify.png)

<!---HONumber=Oct15_HO3-->