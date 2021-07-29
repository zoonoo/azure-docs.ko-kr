---
title: Azure FXT Edge Filer 포트 목록
description: FXT 클러스터 환경에서 사용하는 TCP/UDP 포트 목록
author: ekpgh
ms.author: v-erkel
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: 28de9732f6a22f730059c08b5be939c23e52ebaa
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111415209"
---
# <a name="required-network-ports"></a>필수 네트워크 포트

이 목록에는 FXT 클러스터 환경에 필요한 TCP/UDP 포트가 표시됩니다. 이러한 포트에 액세스할 수 있도록 방화벽을 구성합니다.

시스템의 특정 요구 사항은 사용하는 백 엔드 스토리지의 종류에 따라 달라집니다.

자세한 내용은 Microsoft 서비스 및 지원에 문의합니다.

## <a name="api-ports"></a>API 포트

| Direction | Type | 포트 번호 | 프로토콜 |
|-----------|------|-------------|----------|
| 인바운드   | TCP  | 22          | SSH      |
| 아웃바운드  | TCP  | 80          | HTTP     |
| 인바운드 및 아웃바운드  | TCP  | 443         | HTTPS    |

## <a name="nfs-ports"></a>NFS 포트

인바운드 NFS 포트:

| Type    | 포트 번호 | 서비스  |
|---------|-------------|----------|
| TCP/UDP | 111         | RPCBIND  |
| TCP/UDP | 2049        | NFS      |
| TCP/UDP | 4045        | NLOCKMGR |
| TCP/UDP | 4046        | MOUNTD   |
| TCP/UDP | 4047        | 상태   |

아웃바운드 NFS 포트:

| Type    | 포트 번호 | 서비스  |
|---------|-------------|----------|
| TCP/UDP | 111         | RPCBIND  |
| TCP/UDP | 2049        | NFS      |

아웃바운드 NFS 포트 트래픽은 코어 파일러로 사용되는 스토리지의 종류에 따라 달라집니다. (일부 시스템은 포트 2049를 사용하지 않지만 여기에 나열될 만큼 일반적입니다. 모든 클러스터는 포트 111에서 액세스해야 합니다.) 스토리지 시스템에 대한 설명서를 확인하거나 아래 [추가 포트 요구 사항](#additional-port-requirements)에 설명된 쿼리 기술을 사용합니다.

FXT 노드의 일부 아웃바운드 NFS 트래픽은 임시 포트를 사용합니다. 잘 알려진 포트 위의 아웃바운드 FXT 트래픽은 전송 수준에서 제한해서는 안 됩니다.

## <a name="smb-ports"></a>SMB 포트

| Direction | Type | 포트 번호 | 프로토콜 |
|-----------|------|-------------|----------|
| 인바운드 및 아웃바운드  | UDP  | 137         | NetBIOS  |
| 인바운드   | UDP  | 138         | NetBIOS  |
| 인바운드 및 아웃바운드  | TCP  | 139         | SMB      |
| 인바운드 및 아웃바운드  | TCP  | 445         | SMB      |

<!--| Outbound  | UDP  | 137         | NetBIOS  | 
| Outbound  | TCP  | 139         | SMB      |
| Outbound  | TCP  | 445         | SMB      |
-->

## <a name="general-traffic-ports"></a>일반 트래픽 포트

| Direction | Type    | 포트 번호 | 프로토콜 |
|-----------|---------|-------------|----------|
| 아웃바운드  | TCP/UDP | 53          | DNS      |
| 아웃바운드  | TCP/UDP | 88          | Kerberos |
| 아웃바운드  | UDP     | 123         | NTP      |
| 아웃바운드  | TCP/UDP | 389         | LDAP     |
| 아웃바운드  | TCP     | 686         | LDAPS    |

## <a name="additional-port-requirements"></a>추가 포트 요구 사항

코어 파일러가 추가 포트에 액세스해야 할 수 있습니다. 이 요구 사항은 사용되는 스토리지 유형에 따라 달라집니다.

`rpcinfo` 명령을 사용하여 특정 서버에서 사용하는 포트를 알 수 있습니다. 방화벽이 없는 클라이언트 시스템에서 이 명령을 실행합니다.

`rpcinfo -p <server_IP_address>`

## <a name="next-steps"></a>다음 단계

* Azure FXT Edge Filer 클러스터에 백 엔드 [스토리지를 추가](add-storage.md)하는 방법
* 포트 구성에 대해 자세히 알아보려면 [지원팀에 문의](support-ticket.md)
