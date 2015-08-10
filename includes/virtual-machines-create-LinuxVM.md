1. [Azure CLI에서 Azure에 연결](../articles/xplat-cli-connect.md)에 나열된 단계를 사용하여 Azure 구독에 로그인 합니다.
2. 다음을 사용하여 서비스 관리 모드에 있는지 확인합니다.

        azure config mode asm

3. 사용 가능한 이미지에서 로드하려는 Linux 이미지를 찾습니다.

        azure vm image list | grep "Linux"

4. `azure vm create`을 사용하여 위 목록의 Linux 이미지를 사용하여 새 가상 컴퓨터를 만듭니다. 이 단계에서는 새 클라우드 서비스와 함께 새 저장소 계정도 만듭니다. 가상 컴퓨터를 `-c` 옵션을 사용한 기존 클라우드 서비스에서도 연결할 수 있습니다. `-e` 옵션을 사용한 Linux 가상 컴퓨터에 로그인할 수 있는 SSH 끝점도 만들  
수 있습니다.

        ~$ azure vm create "MyTestVM" b4590d9e3ed742e4a1d46e5424aa335e__suse-opensuse-13.1-20141216-x86-64 "adminUser" -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b4590d9e3ed742e4a1d46e5424aa335e__suse-opensuse-13.1-20141216-x86-64
        Enter VM 'adminUser' password:*********
        Confirm password: *********
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating a new storage account 'mytestvm1437604756125'
        + Creating VM
        info:    vm create command OK

    >[AZURE.NOTE]Linux 가상 컴퓨터에서는, `vm create`에서 `-e` 옵션을 반드시 제공해야 합니다. 가상 컴퓨터를 만든 후에는 SSH 사용이 불가능합니다. SSH에 대한 자세한 내용은 [Azure에서 Linux와 함께 SSH를 사용하는 방법](../articles/virtual-machines/virtual-machines-linux-use-ssh-key.md)을 참조하세요.

    *b4590d9e3ed742e4a1d46e5424aa335e\_\_suse-opensuse-13.1-20141216-x86-64*는 위 단계에 있는 이미지 목록에서 선택한 이미지입니다. *MyTestVM*은 새 가상 컴퓨터의 이름이고, *adminUser*는 가상 컴퓨터 내에서 SSH를 사용하는 사용자 이름입니다. 사용자 요구 사항에 따라 이러한 변수를 대체할 수 있습니다. 이 명령에 대한 자세한 내용은 [Azure 서비스 관리를 포함한 Azure CLI 사용](../articles/virtual-machines/virtual-machines-command-line-tools.md)을 방문하십시오.

5. 다음에 주어진 목록에 새로 만든 Linux 가상 컴퓨터가 나타납니다.

        azure vm list

6. 명령을 사용하여 가상 컴퓨터의 특성을 확인할 수 있습니다.

        azure vm show MyTestVM

7. 새로 만든 가상 컴퓨터는 `azure vm start` 명령을 사용하여 시작하도록 준비되었습니다.

모든 CLI Azure 가상 컴퓨터 명령에 대한 자세한 설명은 [Azure 서비스 관리를 포함한 Azure CLI 사용](../articles/virtual-machines/virtual-machines-command-line-tools.md)을 참조하세요.

<!---HONumber=July15_HO5-->