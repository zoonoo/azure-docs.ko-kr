<properties
	pageTitle="Azure 가상 컴퓨터에 사용자 지정 데이터 삽입"
	description="이 항목에서는 인스턴스를 만들 때 Azure 가상 컴퓨터에 사용자 지정 데이터를 삽입하는 방법과 Windows 또는 Linux에서 사용자 지정 데이터를 찾는 방법에 대해 설명합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management" />

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2015"
	ms.author="rasquill"/>


#Azure 가상 컴퓨터에 사용자 지정 데이터 삽입

프로비전 중에 Azure 가상 컴퓨터에 스크립트 또는 기타 데이터를 삽입하는 경우는 운영 체제가 Microsoft Windows이든, Linux 배포판이든 상관없이 아주 일반적인 시나리오입니다. 이 항목에서는 이러한 방법에 대해 설명합니다.

- 프로비전 중에 Azure 가상 컴퓨터에 데이터 삽입

- Windows 및 Linux에 대해 데이터 검색

- 일부 시스템에서 사용할 수 있는 특수한 도구를 사용하여 사용자 지정 데이터를 자동으로 검색 및 처리

> [AZURE.NOTE]이 문서는 Azure 서비스 관리 계산 스택으로 생성한 VM을 사용하여 사용자 지정 데이터를 삽입하는 방법을 설명합니다. Azure 리소스 관리 계산 스택을 사용하는 방법은 [여기에서 예제 텝플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata)을 참조하세요.

## Azure 가상 컴퓨터에 사용자 지정 데이터 삽입

이 기능은 현재 [Azure 명령줄 인터페이스](https://github.com/Azure/azure-xplat-cli)에서만 지원됩니다. `azure vm create` 명령에 대한 옵션 중 어떤 것도 사용할 수 있지만 다음 접근 방법에서는 기본적인 한 가지 방법만 보여 줍니다.

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## 가상 컴퓨터에서 사용자 지정 데이터 사용

+ Azure 가상 컴퓨터가 Windows 가상 컴퓨터인 경우, 사용자 지정 데이터 파일은 `%SYSTEMDRIVE%\AzureData\CustomData.bin`에 저장되며, 로컬 컴퓨터에서 새 가상 컴퓨터로 전송되기 위해 base64로 인코딩되었더라도 자동으로 디코딩되므로 즉시 열거나 사용할 수 있습니다.

   >[AZURE.NOTE]이 파일이 있으면 덮어쓰여집니다. 디렉터리에 대한 보안은 **시스템:모든 권한** 및 **관리자:모든 권한**으로 설정됩니다.

+ Azure 가상 컴퓨터가 Linux 가상 컴퓨터이면 사용자 지정 데이터 파일은 다음 두 위치에 있지만 데이터가 base64로 인코딩되므로 데이터를 먼저 디코딩해야 합니다.

    + `/var/lib/waagent/ovf-env.xml`에서
    + `/var/lib/waagent/CustomData`에서



## Azure에서 Cloud-Init

Azure 가상 컴퓨터를 CoreOS 또는 Ubuntu 이미지에서 온 경우, CustomData를 사용하여 cloud-config를 cloud-init으로 보낼 수 있습니다. 또는 사용자 지정 데이터 파일이 스크립트인 경우, cloud-init이 스크립트를 실행하기만 하면 됩니다.

### Ubuntu 클라우드 이미지

대부분의 Azure Linux 이미지에서 임시 리소스 디스크와 swap 파일을 구성하도록 "/etc/waagent.conf"를 편집합니다. 자세한 내용은 [Azure Linux 에이전트 사용자 가이드](virtual-machines-linux-agent-user-guide.md)를 참조하세요.

그러나 Ubuntu 클라우드 이미지에서 cloud-init을 사용하여 리소스 디스크(즉, "ephemeral" 디스크) 및 swap 파티션을 구성해야 합니다. 자세한 내용은 Ubuntu wiki에서 다음 페이지를 참조하세요.

 - [Ubuntu Wiki: Swap 파티션 구성](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계: cloud-init 사용

자세한 내용은 [Ubuntu에 대한 cloud-init 설명서](https://help.ubuntu.com/community/CloudInit)(영문)를 참조하세요.

<!--Link references-->
[역할 서비스 관리 REST API 참조 추가](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Azure 명령줄 인터페이스](https://github.com/Azure/azure-sdk-tools-xplat)
 

<!---HONumber=July15_HO3-->