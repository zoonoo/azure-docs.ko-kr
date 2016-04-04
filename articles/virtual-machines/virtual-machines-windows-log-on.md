<properties
	pageTitle="Windows VM에 로그온 | Microsoft Azure"
	description="Azure 포털을 사용하여 리소스 관리자 배포 모델로 만든 Windows 가상 컴퓨터에 로그온합니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="cynthn"/>


# Azure 포털을 사용하여 Windows 가상 컴퓨터에 로그온


Azure 포털에서 **연결** 단추를 사용하여 원격 데스크톱 세션을 시작하고 Windows VM에 로그온합니다.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-connect-logon.md).


## 가상 컴퓨터에 연결

1. Azure 포털에 로그인합니다.

2. **가상 컴퓨터**를 클릭한 다음 가상 컴퓨터를 선택합니다.

3. 페이지 아래쪽의 명령 모음에서 **연결**을 클릭합니다.

	![가상 컴퓨터에 로그온](./media/arm_log_on_windows_vm/rm_windows_connect.png)
	

4. **연결**을 클릭하면 원격 데스크톱 프로토콜 파일(.rdp 파일)이 생성되어 다운로드됩니다. **열기**를 클릭하여 이 파일을 사용합니다.

	![가상 컴퓨터에 로그온](./media/arm_log_on_windows_vm/rm_connect_rdp_file.png)
	
5. 원격 데스크톱 창에서 **연결**을 클릭하여 계속합니다.

	![연결 계속](./media/arm_log_on_windows_vm/rm_connect_unknown.png)

	
6. Windows 보안 창에서 가상 컴퓨터의 계정에 대한 자격 증명을 입력하고 **확인**을 클릭합니다.

 	대부분의 경우 자격 증명은 가상 컴퓨터를 만들 때 지정한 로컬 계정 사용자 이름 및 암호입니다. 도메인은 *vmname*&#92;*username*으로 입력된 가상 컴퓨터의 이름입니다.
	
	가상 컴퓨터가 조직의 도메인에 속하는 경우에는 사용자 이름에 해당 도메인의 이름을 *Domain*&#92;*Username* 형식으로 포함해야 합니다. 계정은 관리자 그룹에 있거나 VM에 대한 원격 액세스 권한을 부여받아야 합니다.
	
	가상 컴퓨터가 도메인 컨트롤러인 경우 해당 도메인에 대한 도메인 관리자 계정의 사용자 이름과 암호를 입력합니다.

7.	**예**를 클릭하여 가상 컴퓨터의 ID를 확인하고 로그온을 완료합니다.

	![컴퓨터의 ID 확인](./media/arm_log_on_windows_vm/rm_connect_cert.png)

## 다음 단계

연결하려고 할 때 문제가 발생할 경우 [Windows 기반 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결](virtual-machines-windows-troubleshoot-rdp-connection.md)을 참조하세요.

<!---HONumber=AcomDC_0323_2016-->