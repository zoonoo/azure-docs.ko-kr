<properties
	pageTitle="Linux 가상 컴퓨터에 LAMP 배포 | Microsoft Azure"
	description="Linux VM에 LAMP 스택 설치 방법을 알아봅니다."
	services="virtual-machines-linux"
	documentationCenter="virtual-machines"
	authors="jluk"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="NA"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="jluk"/>

# Azure에서의 LAMP 스택 배포
이 문서는 Azure에서 Apache 웹 서버, MySQL 및 PHP (LAMP 스택)를 배포하는 방법을 안내합니다. Azure 계정([여기에서 무료 평가판 받기](https://azure.microsoft.com/pricing/free-trial/)) 및 [Azure 계정에 연결된](../xplat-cli-connect.md) [Azure CLI](../xplat-cli-install.md)가 필요합니다.

이 문서에서 다루는 LAMP 설치 방법에는 두 가지가 있습니다.

## 빠른 명령 요약

1) 새 Vm에 LAMP 배포

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

2) 기존 VM에 LAMP 배포

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## 새 Vm에 LAMP 배포 연습

VM을 포함하는 새 [리소스 그룹](../resource-group-overview.md) 만들어서 시작할 수 있습니다.

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

VM 자체를 만들려면 [여기 GitHub에서](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app) 찾을 수 있는 이미 작성된 Azure Resource Manager 템플릿을 사용할 수 있습니다.

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

이미 설치된 LAMP를 사용해 이제 Linux VM을 만들었습니다. 원하는 경우 [LAMP가 성공적으로 설치되었는지 확인하기]로 이동하여 설치를 확인할 수 있습니다.

## 기존 Vm에 LAMP 배포 연습

Linux VM을 만드는 데 도움이 필요한 경우 [Linux VM을 만드는 방법을 알아보려면 여기](./virtual-machines-linux-quick-create-cli.md)로 갈 수 있습니다. 다음으로, Linux VM에 SSH해야 합니다. SSH 키를 만드는 데 도움이 필요한 경우 [Linux/Mac에서 SSH 키를 만드는 방법을 알아보려면 여기](./virtual-machines-linux-mac-create-ssh-keys.md)로 갈 수 있습니다. SSH 키를 이미 있는 경우 계속해서 `ssh username@uniqueDNS`으로 Linux Vm에 SSH합니다.

이제 Linux VM에서 작업할 수 있으므로 Debian 기반 배포에 LAMP 스택 설치를 단계별로 안내합니다. 정확한 명령은 다른 Linux 배포판에서 다를 수도 있습니다.

#### Debian/Ubuntu에 설치

다음 패키지 `apache2`, `mysql-server`, `php5`, `php5-mysql`를 설치해야 합니다. 이러한 패키지를 직접 가져오거나 Tasksel을 사용하여 설치할 수 있습니다. 아래에는 두 옵션 모두에 대한 지침이 나와 있습니다. 설치하기 전에 패키지 목록을 다운로드하고 업데이트해야 합니다.

    user@ubuntu$ sudo apt-get update
    
##### 개별 패키지
apt-get을 사용합니다.

	user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### Tasksel 사용
또는 관련된 여러 패키지를 하나의 조정된 “작업”으로 시스템에 설치하는 Debian/Ubuntu 도구인 Tasksel을 다운로드할 수 있습니다.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

위의 옵션 중 하나를 실행하고 나면 이러한 패키지와 그 밖의 많은 종속성을 설치할 것인지를 묻는 메시지가 표시됩니다. 'y'를 누른 후 'Enter' 키를 눌러 계속 진행하고 화면에 표시되는 메시지에 따라 MySQL에 대한 관리 암호를 설정합니다. 이렇게 하면 PHP와 MySQL을 함께 사용하는 데 필요한 최소한의 PHP 확장이 설치됩니다.

![][1]

다음 명령을 실행하여 패키지로 사용 가능한 다른 PHP 확장을 살펴봅니다.

	user@ubuntu$ apt-cache search php5


#### Info.php 문서 만들기

이제 명령줄에 `apache2 -v`, `mysql -v`, 또는 `php -v`을 입력하여 Apache, MySQL 및 PHP의 버전을 확인할 수 있습니다.

테스트를 추가 하려는 경우 브라우저에서 보려면 빠른 PHP 정보 페이지를 만들 수 있습니다. 이 명령 사용하여 나노 텍스트 편집기로 새 파일을 만듭니다.

    user@ubuntu$ sudo nano /var/www/html/info.php

GNU Nano 텍스트 편집기에서 다음 줄을 추가합니다.

    <?php
    phpinfo();
    ?>

그런 다음 저장하고 텍스트 편집기를 끝냅니다.

모든 새 설치 적용되도록 이 명령을 사용해 Apache를 다시 시작합니다.

    user@ubuntu$ sudo service apache2 restart

## LAMP 설치가 성공적인지 확인

이제 다음과 유사하게 표시될 http://youruniqueDNS/info.php로 가서 브라우저에서 방금 만든 PHP 정보 페이지를 확인할 수 있습니다.

![][2]

http://youruniqueDNS/로 가서 Apache2 Ubuntu 기본 페이지 보기로 Apache 설치를 확인할 수 있습니다. 다음과 유사한 결과가 표시됩니다.

![][3]

축하합니다. 이제 방금 Azure Vm에 LAMP 스택 설정을 마쳤습니다!

## 다음 단계

LAMP 스택에서 Ubuntu 설명서를 확인하세요.

- [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png

<!---HONumber=AcomDC_0824_2016-->