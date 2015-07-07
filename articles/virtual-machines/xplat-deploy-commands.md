<properties
   pageTitle="Mac, Linux 및 Windows용 Azure CLI를 사용하여 템플릿 배포 | Microsoft Azure"
   description="템플릿을 배포하거나 업데이트하는 기본 단계를 설명합니다."
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="infrastructure"
   ms.date="06/09/2015"
   ms.author="danlep"/>

# Mac, Linux 및 Windows용 Azure CLI를 사용하여 템플릿 배포

## curl, wget 또는 기타 다운로드 도구 설치
이 항목에서는 **curl**을 사용합니다. 운영 체제의 패키지 관리자를 사용하거나 [여기](http://curl.haxx.se/download.html)에서 다운로드하세요.

## 템플릿 매개 변수 파일(또는 필요한 경우 템플릿) 다운로드

다음 단계를 통해 Azure 템플릿(복잡한 템플릿 포함) 하나를 배포할 수 있습니다. 이 항목에서는 하나의 예로 설치된 customscript VM 확장을 사용하여 기본 Ubuntu 서버를 만드는 템플릿을 사용합니다. 참조를 위해 항목 끝에도 파일이 포함되어 있습니다.

### azuredeploy-parameters.json 파일 다운로드

배포할 템플릿에 대한 azuredeploy-parameters.json 파일이 있는 경우 다운로드합니다.

    curl -O https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy-parameters.json

## 리소스 그룹 배포 정보 입력

선호하는 편집기에서 이 파일을 엽니다. 여러 키, 특히 **adminUsername**, **adminPassword**(복잡성 규칙 기억) 및 원하는 저장소 계정 이름과 DNS 이름 값을 지정해야 합니다.

    {
      "newStorageAccountName": {
        "value": "uniquestorageaccountname"
      },
      "adminUsername": {
        "value": ""
      },
      "adminPassword": {
        "value": ""
      },
      "dnsNameForPublicIP": {
        "value": "uniquednsnameforpublicip"
      },
      "vmSourceImageName": {
        "value": "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_2_LTS-amd64-server-20150309-ko-kr-30GB"
      },
      "location": {
        "value": "West US"
      },
      "virtualNetworkName": {
        "value": "myVNET"
      },
      "vmSize": {
        "value": "Standard_A0"
      },
      "vmName": {
        "value": "myVM"
      },
      "publicIPAddressName": {
        "value": "myPublicIP"
      },
      "nicName": {
        "value": "myNic"
      }
    }

새 값을 추가(가능한 경우 Azure에서 새 저장소 및 DNS 리소스를 자동으로 만듬)하거나 이미 만든 리소스를 사용합니다. 다음 azuredeploy-parameters.json 파일은 예제를 보여 줍니다.




아래 url은 "빈" azuredeploy-parameters.json에서 매개 변수 파일을 가져옵니다. 이는 대화형 방법을 사용하는 경우에 작동합니다. 다운로드하고 사용자 지정한 매개 변수 파일을 사용하는 경우에는 대신 --template-file <template-file> 옵션을 사용해야 합니다. 또한 수행할 작업에 따라 이러한 파일의 일부에 대한 개별 섹션을 추출하는 스크립트를 작성했습니다. json 구분 문석을 수행하려면 jq: curl http://stedolan.github.io/jq/download/linux64/jq -o /usr/bin/jq가 필요할 수도 있습니다.


### 템플릿 및 매개 변수 파일 배포


[AZURE.NOTE]일부 템플릿에는 해당 azuredeploy-parameters.json 파일이 없을 수 있습니다.

사용 중인 템플릿에 따라 매개 변수를 설정해야 하거나 이미 템플릿 자체의 일부일 수 있습니다. 이 경우 다음과 같이 할 수 있습니다.

템플릿에 직접 해당 매개 변수가 포함되어 있거나 매개 변수 데이터를 직접 추출할 수 있습니다. 템플릿 구조에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 언어](https://msdn.microsoft.com/library/azure/dn835138.aspx)를 참조하세요.


https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy.json(또는 azuredeploy-parameters.json 파일) 템플릿의 parameters 섹션을 추출할 수 있습니다. 이 경우 템플릿 자체 내에 다시 저장하거나 별도의 azuredeploy-parameters.json 파일로 다시 저장해야 합니다. parameters에 배치할 값을 가져와야 합니다.

## azuredeploy-templates.json 파일 수정

필요한 값 수집

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

Vestibul ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia Curae; Nullam ultricies, ipsum vitae volutpat hendrerit, purus diam pretium eros, vitae tincidunt nulla lorem sed turpis: [다른 azure.microsoft.com 설명서 항목에 대한 링크 3](../storage-whatis-account.md).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png
 

<!---HONumber=62-->