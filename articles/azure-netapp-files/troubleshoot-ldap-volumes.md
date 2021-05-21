---
title: Azure NetApp Files의 LDAP 볼륨 문제 해결 | Microsoft Docs
description: Azure NetApp Files의 LDAP 볼륨을 구성할 때 발생할 수 있는 오류 조건에 대한 해결 방법을 설명합니다.
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
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: eea3f691bc6d91948dc73b4a02c89abfac12d384
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498921"
---
# <a name="troubleshoot-ldap-volume-issues"></a>LDAP 볼륨 문제 해결

이 문서에서는 LDAP 볼륨을 구성할 때 발생할 수 있는 오류 조건에 대한 해결 방법을 설명합니다.

## <a name="errors-and-resolutions-for-ldap-volumes"></a>LDAP 볼륨에 대한 오류 및 해결 방법

|     오류 조건    |     해결 방법    |
|-|-|
| ldapEnabled가 true로 설정된 SMB 볼륨을 만드는 동안 오류가 발생했습니다. <br> `Error Message: ldapEnabled option is only supported with NFS protocol volume. ` | LDAP를 사용하도록 설정된 SMB 볼륨을 만들 수 없습니다. <br> LDAP를 사용하지 않도록 설정한 SMB 볼륨을 만듭니다. |
| 기존 볼륨에 대한 ldapEnabled 매개 변수 값을 업데이트하는 동안 오류가 발생했습니다. <br> `Error Message: ldapEnabled parameter is not allowed to update` |  볼륨을 만든 후에는 LDAP 옵션 설정을 수정할 수 없습니다. <br> 만든 볼륨에서 LDAP 옵션 설정을 업데이트하지 마세요. 자세한 내용은 [NFS 볼륨 액세스에 대한 확장 그룹을 사용하여 ADDS LDAP 구성](configure-ldap-extended-groups.md)을 참조하세요. |
| LDAP 지원 NFS 볼륨을 만드는 동안 오류가 발생했습니다. <br> `Could not query DNS server` <br> `Sample error message:` <br> `"log": time="2020-10-21 05:04:04.300" level=info msg=Res method=GET url=/v2/Volumes/070d0d72-d82c-c893-8ce3-17894e56cea3  x-correlation-id=9bb9e9fe-abb6-4eb5-a1e4-9e5fbb838813 x-request-id=c8032cb4-2453-05a9-6d61-31ca4a922d85 xresp="200:  {\"created\":\"2020-10-21T05:02:55.000Z\",\"lifeCycleState\":\"error\",\"lifeCycleStateDetails\":\"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available.\",\"name\":\"smb1\",\"ownerId\ \":\"8c925a51-b913-11e9-b0de-9af5941b8ed0\",\"region\":\"westus2stage\",\"volumeId\":\"070d0d72-d82c-c893-8ce3-` |  이 오류는 DNS에 연결할 수 없기 때문에 발생합니다. <br> <ul><li> Azure NetApp Files에 대해 올바른 사이트(사이트 범위 지정)를 구성했는지 확인합니다. </li><li> DNS에 연결할 수 없는 이유는 잘못된 DNS IP 주소 또는 네트워킹 문제일 수 있습니다. AD 연결에 입력한 DNS IP 주소가 올바른지 확인합니다. </li><li> AD와 볼륨이 동일한 지역 및 동일한 VNet에 있는지 확인합니다. 서로 다른 VNet에 위치한 경우 두 VNet 간에 VNet 피어링이 설정되어 있는지 확인합니다.</li></ul> |
| 스냅샷에서 볼륨을 만드는 동안 오류가 발생했습니다. <br> `Aggregate does not exist` | Azure NetApp Files는 LDAP가 비활성화된 볼륨에 속한 스냅샷에서 새로운 LDAP 지원 볼륨을 프로비전하는 기능을 지원하지 않습니다. <br> 지정된 스냅샷에서 LDAP 사용하지 않도록 설정된 새 볼륨을 만들어 보세요. |

## <a name="next-steps"></a>다음 단계  

* [NFS 볼륨 액세스에 대한 확장 그룹을 사용하여 ADDS LDAP 구성](configure-ldap-extended-groups.md)
* [Azure NetApp Files에 대한 NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files에 대한 이중 프로토콜(NFSv3 및 SMB) 볼륨 만들기](create-volumes-dual-protocol.md)