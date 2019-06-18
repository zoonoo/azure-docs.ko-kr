---
title: Azure의 Linux VM에서 MySQL 설정 | Microsoft Docs
description: Azure Linux 가상 머신(Ubuntu 또는 RedHat 제품군 OS)에 MySQL 스택을 설치하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: 21ad3f9baf4b8e117f881d9a36fc606af04e17a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66158433"
---
# <a name="how-to-install-mysql-on-azure"></a>Azure에 MySQL을 설치하는 방법
이 문서에서는 Linux를 실행하는 Azure 가상 머신에서 MySQL을 설치 및 구성하는 방법을 알아봅니다.


> [!NOTE]
> 이 자습서를 완료하려면 Linux를 실행하는 Microsoft Azure 가상 머신이 이미 있어야 합니다. 계속하기 전에 VM 이름으로 `mysqlnode`를 사용하고 사용자로 `azureuser`를 사용하여 Linux VM을 만들고 설정하려면 [Azure Linux VM 자습서](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
> 
> 

이 경우 3306 포트를 MySQL 포트로 사용합니다.  

이 문서의 예로는 MySQL5.6을 설치하는 리포지토리 패키지를 사용합니다. 실제로, MySQL5.6의 성능은 MySQL5.5보다 많이 개선되었습니다.  자세한 내용은 [여기](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/)를 참조하세요.

## <a name="install-mysql56-on-ubuntu"></a>Ubuntu에 MySQL5.6을 설치 합니다.
Ubuntu를 실행 하는 Linux VM 사용 됩니다.


### <a name="install-mysql"></a>MySQL 설치

으로 전환 하 여 MySQL Server 5.6 설치는 `root` 사용자:

```bash  
sudo su -
```

mysql-server 5.6 설치:

```bash  
apt-get update
apt-get -y install mysql-server-5.6
```

  
설치 하는 동안 아래 MySQL 루트 암호를 설정 하 라는 메시지가 표시 대화 상자 창이 표시 됩니다 하 고 여기에 암호를 설정 해야 합니다.
  
![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

암호를 다시 입력하여 확인합니다.

![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

### <a name="sign-in"></a>로그인
  
MySQL Server 설치가 완료되면 MySQL 서비스가 자동으로 시작됩니다. 사용 하 여 MySQL 서버에 로그인 할 수 있습니다는 `root` 사용자 암호를 입력 합니다.

```bash  
mysql -uroot -p
```


### <a name="manage-the-mysql-service"></a>MySQL 서비스 관리

MySQL 서비스의 상태를 가져옵니다.

```bash   
service mysql status
```
  
Start MySQL Service

```bash  
service mysql start
```
  
MySQL 서비스를 중지 합니다.

```bash  
service mysql stop
```
  
MySQL 서비스를 다시 시작

```bash  
service mysql restart
```

## <a name="install-mysql-on-red-hat-os-centos-oracle-linux"></a>Red Hat OS, CentOS, Oracle Linux에 MySQL 설치
여기에서는 CentOS 또는 Oracle Linux와 함께 Linux VM을 사용합니다.

### <a name="add-the-mysql-yum-repository"></a>MySQL yum 리포지토리 추가
    
전환할 `root` 사용자:

```bash  
sudo su -
```

MySQL 릴리스 패키지 다운로드 및 설치:

```bash  
wget https://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
yum localinstall -y mysql-community-release-el6-5.noarch.rpm
```

### <a name="enable-the-mysql-repository"></a>MySQL 저장소를 사용 하도록 설정
MySQL 리포지토리에서 MySQL5.6 패키지를 다운로드할 수 있도록 아래 파일 편집

```bash  
vim /etc/yum.repos.d/mysql-community.repo
```

  
이 파일의 각 값을 아래와 같이 업데이트:

```  
\# *Enable to use MySQL 5.6*
  
[mysql56-community]
name=MySQL 5.6 Community Server
  
baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
enabled=1
  
gpgcheck=1
  
gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

### <a name="install-mysql"></a>MySQL 설치 

저장소에서 MySQL을 설치 합니다.

```bash  
yum install mysql-community-server
```
  
MySQL RPM 패키지 및 모든 관련된 패키지가 설치 됩니다.


## <a name="manage-the-mysql-service"></a>MySQL 서비스 관리
  
MySQL 서버의 서비스 상태를 확인 합니다.

```bash  
service mysqld status\
```
  
기본 포트의 MySQL 서버가 실행 중인지 여부를 확인 합니다.

```bash  
netstat  –tunlp|grep 3306
```

MySQL 서버를 시작 합니다.

```bash
service mysqld start
```

MySQL 서버를 중지 합니다.

```bash
service mysqld stop
```

설정 될 때 시작 하도록 MySQL 시스템 부팅:

```bash
chkconfig mysqld on
```

## <a name="install-mysql-on-suse-linux"></a>SUSE Linux에 MySQL 설치

여기에서는 OpenSUSE와 Linux VM을 사용합니다.

### <a name="download-and-install-mysql-server"></a>MySQL 서버 다운로드 및 설치
  
아래 명령을 통해 `root` 사용자로 전환합니다.  

```bash  
sudo su -
```
  
MySQL 패키지 다운로드 및 설치:

```bash  
zypper update
zypper install mysql-server mysql-devel mysql
```

### <a name="manage-the-mysql-service"></a>MySQL 서비스 관리
  
MySQL 서버의 상태를 확인 합니다.

```bash  
rcmysql status
```
  
확인 여부를 MySQL 서버의 기본 포트:

```bash  
netstat  –tunlp|grep 3306
```

MySQL 서버를 시작 합니다.

```bash
rcmysql start
```

MySQL 서버를 중지 합니다.

```bash
rcmysql stop
```

설정 될 때 시작 하도록 MySQL 시스템 부팅:

```bash
insserv mysql
```

## <a name="next-step"></a>다음 단계
자세한 내용은 참조는 [MySQL](https://www.mysql.com/) 웹 사이트입니다.

