---
title: Azure NetApp Files에 대 한 NFSv 4.1 Kerberos 볼륨 문제 해결 | Microsoft Docs
description: Azure NetApp Files에 대 한 NFSv 4.1 Kerberos 볼륨 문제를 해결 하는 데 도움이 될 수 있는 오류 메시지 및 해결 방법에 대해 설명 합니다.
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
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98134316"
---
# <a name="troubleshoot-nfsv41-kerberos-volume-issues"></a>NFSv 4.1 Kerberos 볼륨 문제 해결 

이 문서에서는 NFSv 4.1 Kerberos 볼륨을 만들거나 관리할 때 발생할 수 있는 오류 조건에 대 한 해결 방법을 설명 합니다. 

## <a name="error-conditions-and-resolutions"></a>오류 조건 및 해결 방법

|     오류 조건    |     해결 방법    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | Azure NetApp Files는 NFSv3 볼륨의 Kerberos를 지원 하지 않습니다. Kerberos는 NFSv 4.1 프로토콜에 대해서만 지원 됩니다.  |
|`This NetApp account has no configured Active Directory   connections`  |  **KDC IP** 및 **AD 서버 이름** 필드를 사용 하 여 netapp 계정에 대 한 Active Directory를 구성 합니다. 지침은 [Azure Portal 구성을](configure-kerberos-encryption.md#configure-the-azure-portal) 참조 하세요. |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | Azure NetApp Files은 일반 NFSv 4.1 볼륨을 Kerberos NFSv 4.1 볼륨으로 변환 하는 것을 지원 하지 않으며 그 반대의 경우도 마찬가지입니다. |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  예: `smb-test-64d9.contoso.com:/nfs41-vol101` | <ol><li> A/PTR 레코드가 올바르게 설정 되어 있고 서버 이름에 대 한 Active Directory에 존재 하는지 확인 `smb-test-64d9.contoso.com` 합니다. <br> NFS 클라이언트에서 `nslookup` `smb-test-64d9.contoso.com` 이 IP 주소 i p 1 (즉,)로 확인 되 면 `10.1.1.68` `nslookup` i p 1의는 하나의 레코드 (즉,)로만 확인 되어야 합니다 `smb-test-64d9.contoso.com` . `nslookup` I P 1는 여러 이름으로 확인 하지 *않아야* 합니다. </li>  <li>`NFS-<Smb NETBIOS NAME>-<few random characters>`PowerShell 또는 UI를 사용 하 여 AD에서 형식의 NFS 컴퓨터 계정에 대해 AES-256을 설정 합니다. <br> 명령 예: <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>NFS 클라이언트, AD 및 Azure NetApp Files 저장소 소프트웨어의 시간이 서로 동기화 되 고 5 분 정도 오차 범위 내에 있는지 확인 합니다. </li>  <li>명령을 사용 하 여 NFS 클라이언트에서 Kerberos 티켓을 가져옵니다 `kinit <administrator>` .</li> <li>NFS 클라이언트 호스트 이름을 15 자 미만으로 줄이고 영역 조인을 다시 수행 합니다. </li><li>다음과 같이 NFS 클라이언트 및 서비스를 다시 시작 `rpcgssd` 합니다. 명령은 OS에 따라 달라질 수 있습니다.<br> RHEL 7: <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8: <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu: <br> 서비스를 다시 시작 `rpc-gssd` 합니다. <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | 이 문제는 NFS 클라이언트 문제와 관련이 있을 수 있습니다. NFS 클라이언트를 다시 부팅 합니다.    |
|`Hostname lookup failed`   | DNS 서버에 역방향 조회 영역을 만든 다음 해당 역방향 조회 영역에서 AD 호스트 컴퓨터의 PTR 레코드를 추가 해야 합니다. <br> 예를 들어 AD 컴퓨터의 IP 주소가이 고 `10.1.1.4` , 호스트 이름 명령을 사용 하 여 발견 된 ad 컴퓨터의 호스트 이름이이 고, 도메인 이름이 인 것으로 가정 합니다 `AD1` `contoso.com` . 역방향 조회 영역에 추가 된 PTR 레코드는 이어야 합니다 `10.1.1.4 -> AD1.contoso.com` . |
|`Volume creation fails due to unreachable DNS server`  | 사용할 수 있는 두 가지 해결 방법은 다음과 같습니다. <br> <ul><li> 이 오류는 DNS에 연결할 수 없음을 나타냅니다. 잘못 된 DNS IP 또는 네트워킹 문제일 수 있습니다. AD 연결에 입력 된 DNS IP를 확인 하 고 IP가 올바른지 확인 하십시오. </li> <li> AD와 볼륨이 동일한 지역에 있고 동일한 VNet에 있는지 확인 하세요. 서로 다른 Vnet에 있는 경우 두 Vnet 간에 VNet 피어 링이 설정 되어 있는지 확인 합니다. </li></ul> |
|NFSv 4.1 Kerberos 볼륨 만들기가 다음 예제와 비슷한 오류로 인해 실패 합니다. <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |KDC IP가 잘못 되어 Kerberos 볼륨이 만들어졌습니다. 올바른 주소를 사용 하 여 KDC IP를 업데이트 합니다. <br> KDC IP를 업데이트 한 후에는 오류가 발생 하지 않습니다. 볼륨을 다시 만들어야 합니다. |

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 NFSv4.1 Kerberos 암호화 구성](configure-kerberos-encryption.md)
