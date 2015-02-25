<properties pageTitle="Azure 가상 컴퓨터에 사용자 지정 데이터 삽입" description="이 항목에서는 인스턴스를 만들 때 Azure 가상 컴퓨터에 사용자 지정 데이터를 삽입하는 방법과 Windows 또는 Linux에서 사용자 지정 데이터를 찾는 방법에 대해 설명합니다." services="virtual-machines" documentationCenter="" authors="squillace" manager="timlt" editor="tysonn"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="10/1/2014" ms.author="rasquill"/>



#Azure 가상 컴퓨터에 사용자 지정 데이터 삽입 

프로비전 중에 Azure 가상 컴퓨터에 스크립트 또는 기타 데이터를 삽입하는 경우는 운영 체제가 Microsoft Windows이든, Linux 배포판이든 상관없이 아주 일반적인 시나리오입니다. 이 항목에서는 이러한 방법에 대해 설명합니다.

- 프로비전 중에 Azure 가상 컴퓨터에 데이터 삽입

- Windows 및 Linux에 대해 데이터 검색 

- 일부 시스템에서 사용할 수 있는 특수한 도구를 사용하여 사용자 지정 데이터를 자동으로 검색 및 처리

> [AZURE.NOTE] 이 항목은 이 기능에 대한 [이 Azure 블로그 게시물](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)에서 좀 더 자세히 확장되었으며 추가 기능이 나오면 최신 상태로 업데이트될 것입니다.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->
항목 내용

+ [Azure 가상 컴퓨터에 사용자 지정 데이터 삽입](#injectingCustomData)

+ [Azure 가상 컴퓨터에 사용자 지정 데이터 삽입](#usingCustomData)

+ [다음 단계](#nextsteps)


## <a id="injectingCustomData"></a>Azure 가상 컴퓨터에 사용자 지정 데이터 삽입

이 기능은 현재 [Microsoft Azure 플랫폼 간 명령줄 인터페이스](https://github.com/Azure/azure-sdk-tools-xplat)에서만 지원됩니다.  `azure vm create` 명령에 대한 옵션 중 어떤 것도 사용할 수 있지만 다음 접근 방법에서는 기본적인 한 가지 방법만 보여 줍니다. 

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## <a id="usingCustomData"></a>Azure 가상 컴퓨터에 사용자 지정 데이터 삽입
 
+ Azure 가상 컴퓨터가 Windows 가상 컴퓨터인 경우 사용자 지정 데이터 파일은  `%SYSTEMDRIVE%\AzureData\CustomData.bin`에 저장되며, 로컬 컴퓨터에서 새 가상 컴퓨터로 전송되기 위해 base64로 인코딩되었더라도 자동으로 디코딩되므로 즉시 열거나 사용할 수 있습니다. 

   > [AZURE.NOTE] 이 파일이 있으면 덮어쓰여집니다. 디렉터리에 대한 보안은 **시스템:모든 권한** 및 **관리자:모든 권한**으로 설정됩니다.

+ Azure 가상 컴퓨터가 Linux 가상 컴퓨터이면 사용자 지정 데이터 파일은 다음 두 위치에 있지만 데이터가 base64로 인코딩되므로 데이터를 먼저 디코딩해야 합니다.

    +  `/var/lib/waagent/ovf-env.xml`에서
    +  `/var/lib/waagent/CustomData`에서 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a id="nextsteps"></a>다음 단계 cloud-init 사용

Azure 가상 컴퓨터가 Ubuntu 이미지이면 cloud-init를 사용하여 사용자 지정 데이터를 자동으로 사용하는 스크립트를 실행할 수 있습니다(또는 사용자 지정 데이터 파일이 스크립트인 경우 해당 스크립트 실행). 자세한 내용은 [Ubuntu에 대한 cloud-init 설명서](https://help.ubuntu.com/community/CloudInit)를 참조하세요.

<!--Link references-->
[Add Role 서비스 관리 REST API 참조](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Microsoft Azure 플랫폼 간 명령줄 인터페이스](https://github.com/Azure/azure-sdk-tools-xplat)



<!--HONumber=42-->
