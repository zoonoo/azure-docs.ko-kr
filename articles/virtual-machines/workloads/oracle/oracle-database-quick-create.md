---
title: "Azure VM에서 Oracle 12c 데이터베이스 만들기 | Microsoft Docs"
description: "Azure 환경에서 Oracle 12c 데이터베이스를 신속하게 가동하고 실행합니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tonyguid
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/26/2017
ms.author: rclaus
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: ba55e2e4449737c4b759211cf0c429d42b961a38
ms.contentlocale: ko-kr
ms.lasthandoff: 04/29/2017

---

# <a name="create-an-oracle-12c-database-on-azure-vm"></a>Azure VM에서 Oracle 12c 데이터베이스 만들기

이 스크립트는 Azure CLI를 사용하여 Oracle 12c 데이터베이스를 만듭니다.

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI가 사용됩니다. 이 가이드에서는 Azure CLI를 사용하여 Marketplace 갤러리 이미지에서 Oracle 12c 데이터베이스를 배포하는 방법을 자세히 설명합니다.

시작하기 전에 Azure CLI가 설치되었는지 확인합니다. 자세한 내용은 [Azure CLI 설치 가이드](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="log-in-to-azure"></a>Azure에 로그인 

[az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제는 `westus` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기

[az vm create](/cli/azure/vm#create) 명령을 사용하여 VM을 만듭니다. 

다음 예제에서는 `myVM`이라는 VM을 만들고 기본 키 위치에 SSH 키가 없는 경우 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.  

```azurecli
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys
```

VM을 만든 경우 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다. `publicIpAddress`를 적어둡니다. 이 주소는 VM에 액세스하는 데 사용됩니다.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-virtual-machine"></a>가상 컴퓨터에 연결

다음 명령을 사용하여 가상 컴퓨터와의 SSH 세션을 만듭니다. 해당 IP 주소를 가상 컴퓨터의 `publicIpAddress`로 바꿉니다.

```bash 
ssh <publicIpAddress>
```

## <a name="create-database"></a>데이터베이스 만들기

Oracle 소프트웨어는 Marketplace 이미지에 이미 설치되어 있으므로 다음 단계에서 데이터베이스를 설치합니다. 첫 번째 단계는 'oracle' superuser로 실행하고 다음을 로깅하기 위해 수신기를 초기화합니다.

```bash
sudo su - oracle
[oracle@myVM /]$ lsnrctl start
Copyright (c) 1991, 2014, Oracle.  All rights reserved.

Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

TNSLSNR for Linux: Version 12.1.0.2.0 - Production
Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
STATUS of the LISTENER
------------------------
Alias                     LISTENER
Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
Start Date                23-MAR-2017 15:32:08
Uptime                    0 days 0 hr. 0 min. 0 sec
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
The listener supports no services
The command completed successfully
```

다음 단계에서는 데이터베이스를 만듭니다.

```bash
[oracle@myVM /]$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs

Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
```

## <a name="preparing-for-connectivity"></a>연결 준비 
데이터베이스가 올바르게 초기화되었는지 확인하기 위해 로컬 연결을 테스트하려고 합니다. 이 작업을 수행하는 가장 쉬운 방법은 `sqlplus`에 연결하는 것입니다.  연결하기 전에 먼저 일부 환경 변수를 설정해야 합니다. 특히 *ORACLE_HOME* 및 *ORACLE_SID* 환경 변수를 설정해야 합니다.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME

ORACLE_SID=cdb1; export ORACLE_SID
```

필요에 따라 .bashrc 파일에 ORACLE_HOME 및 ORACLE_SID를 추가하여 후속 로그인을 위해 이러한 설정을 저장할 수 있습니다.

```
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1

# add oracle sid
export ORACLE_SID=cdb1

```

## <a name="setup-connectivity-to-oracle-em-express"></a>Oracle EM Express에 대한 설정 연결

Oracle EM Express에서 데이터베이스를 탐색하는 GUI 관리 도구를 사용할 수 있게 됩니다.  Oracle EM Express에 연결하려면 먼저 Oracle에서 포트를 구성해야 합니다.

```bash
$ sudo su - oracle

sqlplus / as sysdba

SQL*Plus: Release 12.1.0.2.0 Production on Fri Apr 7 13:16:30 2017

Copyright (c) 1982, 2014, Oracle.  All rights reserved.


Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL> select con_id, name, open_mode from v$pdbs;

    CON_ID NAME                           OPEN_MODE
---------- ------------------------------ ----------
         2 PDB$SEED                       READ ONLY
         3 PDB1                           MOUNT

SQL> alter session set container=pdb1;

Session altered.

SQL> alter database open;

database opened.

SQL> alter session set container=pdb1;

Session altered.

SQL> exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);

PL/SQL procedure successfully completed.
```
## <a name="automating-database-startup-and-shutdown"></a>데이터베이스 시작 및 종료 자동화

Oracle 인스턴스가 만들어진 후에는 컴퓨터 부팅 시 자동으로 시작되도록 설정되지 않습니다.  이러한 작업을 수행하려면 루트 권한으로 로그인한 후 일부 시스템 파일을 생성/업데이트해야 합니다.

```bash
# sudo su -
```

기본값 'N'에서 'Y'로 "/etc/oratab" 파일 업데이트

```
cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
```

그런 후 "/etc/init.d/dbora" 파일 만들기

```bash
#!/bin/sh
# chkconfig: 345 99 10
# description: Oracle auto start-stop script.
#
# Set ORA_HOME to be equivalent to the $ORACLE_HOME
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle

case "$1" in
'start')
    # Start the Oracle databases:
    # The following command assumes that the oracle login
    # will not prompt the user for any values
    # Remove "&" if you don't want startup as a background process.
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
    touch /var/lock/subsys/dbora
    ;;

'stop')
    # Stop the Oracle databases:
    # The following command assumes that the oracle login
    # will not prompt the user for any values
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
    rm -f /var/lock/subsys/dbora
    ;;
esac
```

사용 권한 변경

```bash
# chgrp dba /etc/init.d/dbora
# chmod 750 /etc/init.d/dbora
```
시작 및 종료에 대한 기호 링크 만들기

```bash
# ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
# ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
# ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
```

테스트할 VM 다시 시작
```bash
# reboot
```

## <a name="opening-the-ports-for-connectivity"></a>연결을 위한 포트 열기

마지막 작업은 외부 끝점을 구성하는 것입니다. VM을 보호하는 Azure 네트워크 보안 그룹을 구성하기 위해 VM에서 SSH 세션을 종료합니다. Oracle DB에 원격으로 액세스하기 위한 끝점을 열려면 다음 명령을 실행합니다. 

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVmNSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

결과가 다음 응답과 유사하게 나타납니다.

```
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

Oracle EM Express에 원격으로 액세스하기 위한 끝점을 열려면 다음 명령을 실행합니다.

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVmNSG --name allow-oracle-EM\
    --protocol tcp --direction inbound --priority 1001 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 5502 --access allow
```

결과가 다음 응답과 유사하게 나타납니다.

```azurecli
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "5502",
  "direction": "Inbound",
  "etag": "W/\"06c68b5e-1b3f-4ae0-bcf6-59b3b981d685\"",
  "id": "/subscriptions/2dad32d6-b188-49e6-9437-ca1d51cec4dd/resourceGroups/kennyRG/providers/Microsoft.Network/networkSecurityGroups/kennyVM1NSG/securityRules/allow-oracle-EM",
  "name": "allow-oracle-EM",
  "priority": 1001,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

브라우저에서 EM Express 연결
```
https://<VM hostname>:5502/em
```
설치 중 지정한 암호로 SYS 계정을 사용하여 로그인 할 수 있습니다.


## <a name="delete-virtual-machine"></a>가상 컴퓨터 삭제

더 이상 필요하지 않은 경우 다음 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

[고가용성 가상 컴퓨터 만들기 자습서](../../linux/create-cli-complete.md)

[VM 배포 CLI 샘플 탐색](../../linux/cli-samples.md)

