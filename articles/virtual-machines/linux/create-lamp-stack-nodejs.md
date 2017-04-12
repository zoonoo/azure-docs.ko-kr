---
title: "Azure CLI 1.0을 사용하여 Linux 가상 컴퓨터에 LAMP 배포 | Microsoft Docs"
description: "Azure의 Linux VM에 LAMP 스택 설치 방법을 알아보기"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jluk
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: NA
ms.topic: article
ms.date: 2/21/2017
ms.author: juluk
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: feba2fb20d1831e92358ff5d1b4c9589d63d28dc
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-lamp-stack-with-the-azure-cli-10"></a>Azure CLI 1.0을 사용하여 LAMP 스택 배포
이 문서에서는 Azure에서 Apache 웹 서버, MySQL 및 PHP(LAMP 스택)를 배포하는 방법을 설명합니다. Azure 계정([무료 평가판 받기](https://azure.microsoft.com/pricing/free-trial/)) 및 [Azure 계정에 연결된](../../xplat-cli-connect.md) [Azure CLI](../../cli-install-nodejs.md)가 필요합니다.

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0] - 클래식 및 리소스 관리 배포 모델용 CLI(이 문서)
- [Azure CLI 2.0](create-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 리소스 관리 배포 모델용 차세대 CLI

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

* 기존 VM에 LAMP 배포

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>새 VM에 LAMP 배포 연습
새 VM을 포함하는 [리소스 그룹](../../azure-resource-manager/resource-group-overview.md) 만들어서 시작할 수 있습니다.

    $ azure group create uniqueResourceGroup westus
    info:    Executing command group create
    info:    Getting resource group uniqueResourceGroup
    info:    Creating resource group uniqueResourceGroup
    info:    Created resource group uniqueResourceGroup
    data:    Id:                  /subscriptions/########-####-####-####-############/resourceGroups/uniqueResourceGroup
    data:    Name:                uniqueResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

VM 자체를 만들려면 [여기 GitHub에서](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)찾을 수 있는 이미 작성된 Azure Resource Manager 템플릿을 사용할 수 있습니다.

    $ azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json uniqueResourceGroup uniqueLampName

입력을 몇 가지 더 하라는 메시지가 담긴 응답을 보게 될 것입니다.

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    storageAccountNamePrefix: lampprefix
    location: westus
    adminUsername: someUsername
    adminPassword: somePassword
    mySqlPassword: somePassword
    dnsLabelPrefix: azlamptest
    info:    Initializing template configurations and parameters
    info:    Creating a deployment
    info:    Created template deployment "uniqueLampName"
    info:    Waiting for deployment to complete
    data:    DeploymentName     : uniqueLampName
    data:    ResourceGroupName  : uniqueResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          :
    data:    Mode               : Incremental
    data:    CorrelationId      : d51bbf3c-88f1-4cf3-a8b3-942c6925f381
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    DeploymentParameters :
    data:    Name                      Type          Value
    data:    ------------------------  ------------  -----------
    data:    storageAccountNamePrefix  String        lampprefix
    data:    location                  String        westus
    data:    adminUsername             String        someUsername
    data:    adminPassword             SecureString  undefined
    data:    mySqlPassword             SecureString  undefined
    data:    dnsLabelPrefix            String        azlamptest
    data:    ubuntuOSVersion           String        14.04.2-LTS
    info:    group deployment create command OK

이미 설치된 LAMP를 사용해 이제 Linux VM을 만들었습니다. 원하는 경우 [LAMP가 성공적으로 설치되었는지 확인](#verify-lamp-successfully-installed)으로 이동하여 설치를 확인할 수 있습니다.

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>기존 VM에 LAMP 배포 연습
Linux VM을 만드는 데 도움이 필요한 경우 [Linux VM을 만드는 방법을 알아보려면 여기](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)로 이동할 수 있습니다. 다음으로, Linux VM에 SSH해야 합니다. SSH 키를 만드는 데 도움이 필요한 경우 [Linux/Mac에서 SSH 키를 만드는 방법을 알아보려면 여기](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)로 이동할 수 있습니다.
SSH 키가 있는 경우 계속해서 `ssh exampleUsername@exampleDNS`을 사용하여 Linux VM에 SSH합니다.

이제 Linux VM 내에서 작업할 수 있으므로 Debian 기반 배포에 LAMP 스택을 설치하는 방법을 설명합니다. 정확한 명령은 다른 Linux 배포판에서 다를 수도 있습니다.

#### <a name="installing-on-debianubuntu"></a>Debian/Ubuntu에 설치
`apache2`, `mysql-server`, `php5`, `php5-mysql` 패키지를 설치해야 합니다. 이러한 패키지를 직접 가져오거나 Tasksel을 사용하여 설치할 수 있습니다. 아래에는 두 옵션 모두에 대한 지침이 나와 있습니다.
설치하기 전에 패키지 목록을 다운로드하고 업데이트해야 합니다.

    user@ubuntu$ sudo apt-get update

##### <a name="individual-packages"></a>개별 패키지
apt-get을 사용합니다.

    user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### <a name="using-tasksel"></a>Tasksel 사용
또는 관련된 여러 패키지를 하나의 조정된 “작업”으로 시스템에 설치하는 Debian/Ubuntu 도구인 Tasksel을 다운로드할 수 있습니다.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

이전의 옵션 중 하나를 실행하고 나면 이러한 패키지와 다양한 다른 종속성을 설치할지를 묻는 메시지가 표시됩니다. 'y'를 누른 후 'Enter' 키를 눌러 계속 진행하고 화면에 표시되는 메시지에 따라 MySQL에 대한 관리 암호를 설정합니다. 이렇게 하면 PHP와 MySQL을 함께 사용하는 데 필요한 최소한의 PHP 확장이 설치됩니다. 

![][1]

다음 명령을 실행하여 패키지로 사용 가능한 다른 PHP 확장을 살펴봅니다.

    user@ubuntu$ apt-cache search php5


#### <a name="create-infophp-document"></a>Info.php 문서 만들기
이제 명령줄에 `apache2 -v`, `mysql -v`, 또는 `php -v`을 입력하여 Apache, MySQL 및 PHP의 버전을 확인할 수 있습니다.

테스트를 추가 하려는 경우 브라우저에서 보려면 빠른 PHP 정보 페이지를 만들 수 있습니다. 이 명령을 사용하여 Nano 텍스트 편집기로 파일을 만듭니다.

    user@ubuntu$ sudo nano /var/www/html/info.php

GNU Nano 텍스트 편집기에서 다음 줄을 추가합니다.

    <?php
    phpinfo();
    ?>

그런 다음 저장하고 텍스트 편집기를 끝냅니다.

모든 새 설치 항목이 적용되도록 이 명령을 사용하여 Apache를 다시 시작합니다.

    user@ubuntu$ sudo service apache2 restart

## <a name="verify-lamp-successfully-installed"></a>LAMP 설치가 성공적인지 확인
이제 브라우저를 열고 http://youruniqueDNS/info.php로 이동하여 만든 PHP 정보 페이지를 확인할 수 있습니다. 이 이미지와 유사하게 표시됩니다.

![][2]

http://youruniqueDNS/로 이동하여 Apache2 Ubuntu 기본 페이지 보기로 Apache 설치를 확인할 수 있습니다. 다음 이미지와 유사하게 표시됩니다.

![][3]

축하합니다. 이제 방금 Azure Vm에 LAMP 스택 설정을 마쳤습니다!

## <a name="next-steps"></a>다음 단계
LAMP 스택에서 Ubuntu 설명서를 확인하세요.

* [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/deploy-lamp-stack/apachesuccesspage.png
