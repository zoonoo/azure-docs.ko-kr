
1. 아직 로그인하지 않은 경우 [Azure 관리 포털](http://manage.windowsazure.com)에 로그인합니다.

2. **가상 컴퓨터**를 클릭하고 해당 가상 컴퓨터를 선택합니다.

3. 명령 모음에서 **연결**을 클릭합니다.

	![가상 컴퓨터에 로그온](./media/virtual-machines-log-on-win-server/connectwindows.png)

4. **열기**를 클릭하여 가상 컴퓨터에 대해 자동으로 만들어진 원격 데스크톱 프로토콜 파일을 사용합니다.
	
5. **연결**을 클릭하여 계속합니다.

	![연결 계속](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. 가상 컴퓨터에서 관리자 계정의 자격 증명을 입력한 후 **확인**을 클릭합니다.

 >[AZURE.TIP]대부분의 경우 가상 컴퓨터를 만들 때 지정한 사용자 이름 및 암호를 사용합니다. 사용자 이름을 확인하여 올바른 도메인 정보가 있는지 권한이 있는지 알아봅니다.

>- VM이 조직의 도메인에 속하는 경우에는 사용자 이름에 해당 도메인의 이름을 포함해야 합니다.
- VM이 도메인에 속하지 않은 경우 ''로 시작하여 도메인 정보를 제거하거나 VM 이름을 도메인 이름으로 사용합니다. 예를 들면 `\MyUserName` 또는 `MyTestVM\MyUserName`과 같습니다. 
- VM이 도메인 컨트롤러인 경우 해당 도메인의 도메인 관리자의 사용자 이름과 암호를 입력합니다.

**예**를 클릭하여 가상 컴퓨터의 ID를 확인합니다.

![컴퓨터의 ID 확인](./media/virtual-machines-log-on-win-server/connectverify.png)

이제 원격으로 가상 컴퓨터를 사용할 수 있습니다.

<!---HONumber=August15_HO6-->