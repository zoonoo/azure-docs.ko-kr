---
title: Azure NetApp Files에 대한 NFSv4.1 Kerberos 볼륨 문제 해결 | Microsoft Docs
description: Azure NetApp Files에 대한 NFSv4.1 Kerberos 볼륨 문제를 해결하는 데 도움이 되는 오류 메시지 및 해결 방법에 대해 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.author: b-juche
ms.openlocfilehash: 638607da02b1db4842cdc04f86a4fed1860c243f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98134316"
---
# <a name="troubleshoot-nfsv41-kerberos-volume-issues"></a>NFSv4.1 Kerberos 볼륨 문제 해결 

이 문서에서는 NFSv4.1 Kerberos 볼륨을 만들거나 관리할 때 발생할 수 있는 오류 조건에 대한 해결 방법을 설명합니다. 

## <a name="error-conditions-and-resolutions"></a>오류 조건 및 해결 방법

|     오류 조건    |     해결 방법    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | Azure NetApp Files는 NFSv3 볼륨에 대해 Kerberos를 지원하지 않습니다. Kerberos는 NFSv4.1 프로토콜에 대해서만 지원됩니다.  |
|`This NetApp account has no configured Active Directory   connections`  |  **KDC IP** 및 **AD 서버 이름** 필드를 사용하여 NetApp 계정에 대한 Active Directory를 구성합니다. 지침은 [Azure Portal 구성](configure-kerberos-encryption.md#configure-the-azure-portal)을 참조하세요. |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | Azure NetApp Files는 일반 NFSv4.1 볼륨을 Kerberos NFSv4.1 볼륨으로, 또는 그 반대로 변환하는 것을 지원하지 않습니다. |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  예: `smb-test-64d9.contoso.com:/nfs41-vol101` | <ol><li> A/PTR 레코드가 올바르게 설정되고 서버 이름 `smb-test-64d9.contoso.com`에 대해 Active Directory에 존재하는지 확인합니다. <br> NFS 클라이언트에서 `smb-test-64d9.contoso.com`의 `nslookup`이 IP 주소 IP1(즉, `10.1.1.68`)로 확인되면 IP1의 `nslookup`은 하나의 레코드(즉, `smb-test-64d9.contoso.com`)로만 확인되어야 합니다. IP1의 `nslookup`은 *절대* 여러 이름으로 확인되어서는 안 됩니다. </li>  <li>PowerShell 또는 UI를 사용하여 AD에서 유형 `NFS-<Smb NETBIOS NAME>-<few random characters>`의 NFS 컴퓨터 계정에 대해 AES-256을 설정합니다. <br> 명령 예: <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>NFS 클라이언트, AD 및 Azure NetApp Files 스토리지 소프트웨어의 시간이 서로 동기화되고 5분 오차 범위 내에 있는지 확인합니다. </li>  <li>`kinit <administrator>` 명령을 사용하여 NFS 클라이언트에서 Kerberos 티켓을 가져옵니다.</li> <li>NFS 클라이언트 호스트 이름을 15자 미만으로 줄이고 영역 결합을 다시 수행합니다. </li><li>다음과 같이 NFS 클라이언트와 `rpcgssd` 서비스를 다시 시작합니다. 명령어는 OS에 따라 다를 수 있습니다.<br> RHEL 7: <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8: <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu: <br> (`rpc-gssd` 서비스를 다시 시작합니다.) <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | 이 문제는 NFS 클라이언트 문제와 관련이 있을 수 있습니다. NFS 클라이언트를 다시 부팅합니다.    |
|`Hostname lookup failed`   | DNS 서버에 역방향 조회 영역을 만든 다음 해당 역방향 조회 영역에 AD 호스트 컴퓨터의 PTR 레코드를 추가해야 합니다. <br> 예를 들어, AD 시스템의 IP 주소가 `10.1.1.4`이고 AD 시스템의 호스트 이름(hostname 명령을 사용하여 찾은 대로)이 `AD1`이고 도메인 이름이 `contoso.com`이라고 가정합니다. 역방향 조회 영역에 추가된 PTR 레코드는 `10.1.1.4 -> AD1.contoso.com`이어야 합니다. |
|`Volume creation fails due to unreachable DNS server`  | 사용 가능한 솔루션은 두 가지입니다. <br> <ul><li> 이 오류는 DNS에 연결할 수 없음을 나타냅니다. 잘못된 DNS IP 또는 네트워킹 문제 때문일 수 있습니다. AD 연결에서 입력한 DNS IP를 확인하고 IP가 맞는지 확인하세요. </li> <li> AD와 볼륨이 동일한 지역 및 동일한 VNet에 있는지 확인합니다. 서로 다른 VNet에 있는 경우 두 VNet 간에 VNet 피어링이 설정되어 있는지 확인합니다. </li></ul> |
|NFSv4.1 Kerberos 볼륨 만들기가 다음 예와 유사한 오류로 인해 실패합니다. <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |KDC IP가 잘못되었으며 Kerberos 볼륨이 만들어졌습니다. KDC IP를 올바른 주소로 업데이트합니다. <br> KDC IP를 업데이트한 후에도 오류가 사라지지 않습니다. 볼륨을 다시 만들어야 합니다. |

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 NFSv4.1 Kerberos 암호화 구성](configure-kerberos-encryption.md)
