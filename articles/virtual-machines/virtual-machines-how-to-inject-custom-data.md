<properties
	pageTitle="가상 컴퓨터에 사용자 지정 데이터 삽입 | Microsoft Azure"
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
	ms.date="12/08/2015"
	ms.author="rasquill"/>


#Azure 가상 컴퓨터에 사용자 지정 데이터 삽입

프로비전 중에 Azure 가상 컴퓨터에 스크립트 또는 기타 데이터를 삽입하는 경우는 운영 체제가 Windows이든, Linux 배포판이든 상관없이 아주 일반적인 시나리오입니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.


이 항목에서는 이러한 방법에 대해 설명합니다.

- 프로비전 중에 Azure 가상 컴퓨터에 데이터 삽입

- Windows 및 Linux에 대해 데이터 검색

- 일부 시스템에서 사용할 수 있는 특수한 도구를 사용하여 사용자 지정 데이터를 자동으로 검색 및 처리

> [AZURE.NOTE]이 문서는 Azure 서비스 관리 API로 생성한 VM을 사용하여 사용자 지정 데이터를 삽입하는 방법을 설명합니다. Azure 리소스 관리 API를 사용하는 방법은 [예제 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata)을 참조하세요.

## Azure 가상 컴퓨터에 사용자 지정 데이터 삽입

이 기능은 현재 [Azure 명령줄 인터페이스](https://github.com/Azure/azure-xplat-cli)에서만 지원됩니다. `azure vm create` 명령에 대한 모든 옵션을 사용할 수 있지만 다음 예제에서는 한 가지 기본적인 방법을 보여 줍니다.

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## 가상 컴퓨터에서 사용자 지정 데이터 사용

+ Azure 가상 컴퓨터가 Windows 기반 가상 컴퓨터인 경우 사용자 지정 데이터 파일은 `%SYSTEMDRIVE%\AzureData\CustomData.bin`에 저장됩니다. 로컬 컴퓨터에서 새 가상 컴퓨터로 전송하기 위해 base64로 인코딩되었더라도 자동으로 디코딩되므로 즉시 열거나 사용할 수 있습니다.

   >[AZURE.NOTE]이 파일이 있으면 덮어쓰여집니다. 디렉터리에 대한 보안은 **시스템:모든 권한** 및 **관리자:모든 권한**으로 설정됩니다.

+ Azure 가상 컴퓨터가 Linux 기반 가상 컴퓨터인 경우 사용자 지정 데이터 파일은 다음 두 곳에 있습니다. 데이터가 base64로 인코딩되므로 먼저 데이터를 디코딩해야 합니다.

    + `/var/lib/waagent/ovf-env.xml`에서
    + `/var/lib/waagent/CustomData`에서



## Azure에서 Cloud-Init

Azure 가상 컴퓨터를 CoreOS 또는 Ubuntu 이미지에서 온 경우, CustomData를 사용하여 cloud-config를 cloud-init으로 보낼 수 있습니다. 또는 사용자 지정 데이터 파일이 스크립트인 경우, cloud-init이 스크립트를 실행하기만 하면 됩니다.

### Ubuntu 클라우드 이미지

대부분의 Azure Linux 이미지에서 임시 리소스 디스크와 swap 파일을 구성하도록 "/etc/waagent.conf"를 편집합니다. 자세한 내용은 [Azure Linux 에이전트 사용자 가이드](virtual-machines-linux-agent-user-guide.md)를 참조하세요.

그러나 Ubuntu 클라우드 이미지에서 cloud-init을 사용하여 리소스 디스크(즉, "ephemeral" 디스크) 및 swap 파티션을 구성해야 합니다. 자세한 내용은 Ubuntu wiki에서 [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) 페이지를 참조하세요.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계: cloud-init 사용

자세한 내용은 [Ubuntu에 대한 cloud-init 설명서](https://help.ubuntu.com/community/CloudInit)(영문)를 참조하세요.

<!--Link references-->
[역할 서비스 관리 REST API 참조 추가](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Azure 명령줄 인터페이스](https://github.com/Azure/azure-xplat-cli)

<!---HONumber=AcomDC_1217_2015-->