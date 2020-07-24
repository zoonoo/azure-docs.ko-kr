---
title: Azure HPC 캐시 NFS 저장소 대상 문제 해결
description: NFS 저장소 대상을 만들 때 오류를 발생 시킬 수 있는 구성 오류 및 기타 문제를 방지 하 고 해결 하기 위한 팁
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2020
ms.author: v-erkel
ms.openlocfilehash: efa163a2c10a7dc93bf5d26865a0e7eb43f11dea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082769"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>NAS 구성 및 NFS 저장소 대상 문제 해결

이 문서에서는 몇 가지 일반적인 구성 오류에 대 한 솔루션을 제공 하 고, Azure HPC 캐시에서 NFS 저장소 시스템을 저장소 대상으로 추가 하지 못하게 하는 기타 문제에 대해 설명 합니다.

이 문서에는 포트를 확인 하는 방법과 NAS 시스템에 대 한 루트 액세스를 설정 하는 방법에 대 한 정보가 포함 되어 있습니다. 또한 NFS 저장소 대상 생성에 실패할 수 있는 보다 일반적인 문제에 대 한 자세한 정보도 포함 합니다.

> [!TIP]
> 이 가이드를 사용 하기 전에 [NFS 저장소 대상에 대 한 필수 구성 요소](hpc-cache-prerequisites.md#nfs-storage-requirements)를 참조 하세요.

문제에 대 한 해결 방법이 여기에 포함 되지 않은 경우 Microsoft 서비스 및 지원 서비스에서 문제를 조사 하 고 해결 하는 데 도움이 되도록 [지원 티켓을 여세요](hpc-cache-support-ticket.md) .

## <a name="check-port-settings"></a>포트 설정 확인

Azure HPC 캐시는 백 엔드 NAS 저장소 시스템의 여러 UDP/TCP 포트에 대 한 읽기/쓰기 액세스 권한이 필요 합니다. 이러한 포트는 NAS 시스템에서 액세스할 수 있는지 확인 하 고, 저장소 시스템과 캐시 서브넷 간의 모든 방화벽을 통해 이러한 포트에 대해 트래픽이 허용 되는지 확인 합니다. 데이터 센터에 대 한 방화벽 및 네트워크 관리자와 협력 하 여이 구성을 확인 해야 할 수도 있습니다.

포트는 다른 공급 업체의 저장소 시스템에 대해 다르므로 저장소 대상을 설정할 때 시스템의 요구 사항을 확인 하십시오.

일반적으로 캐시는 다음 포트에 대 한 액세스 권한이 필요 합니다.

| 프로토콜 | 포트  | 서비스  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | mountd   |
| TCP/UDP  | 4047  | 상태   |

시스템에 필요한 특정 포트를 알아보려면 다음 ``rpcinfo`` 명령을 사용 합니다. 아래 명령은 포트를 나열 하 고 테이블에 관련 결과의 형식을 지정 합니다. ( *<storage_IP>* 용어 대신 시스템의 IP 주소를 사용 합니다.)

NFS 인프라가 설치 된 모든 Linux 클라이언트에서이 명령을 실행할 수 있습니다. 클러스터 서브넷 내에서 클라이언트를 사용 하는 경우 서브넷과 저장소 시스템 간의 연결을 확인 하는 데도 도움이 될 수 있습니다.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

쿼리에서 반환 된 모든 포트가 ``rpcinfo`` AZURE HPC 캐시의 서브넷에서 무제한 트래픽을 허용 하는지 확인 합니다.

NAS 자체와 저장소 시스템과 캐시 서브넷 간의 모든 방화벽에서 이러한 설정을 확인 합니다.

## <a name="check-root-access"></a>루트 액세스 확인

저장소 대상을 만들려면 Azure HPC 캐시에서 저장소 시스템의 내보내기에 대 한 액세스 권한이 필요 합니다. 특히 내보내기를 사용자 ID 0으로 탑재 합니다.

여러 저장소 시스템에서 다른 방법을 사용 하 여이 액세스를 사용 하도록 설정 합니다.

* Linux 서버는 일반적으로 ``no_root_squash`` 의 내보낸 경로에 추가 ``/etc/exports`` 됩니다.
* 일반적으로 NetApp 및 EMC 시스템은 특정 IP 주소 또는 네트워크에 연결 된 내보내기 규칙을 사용 하 여 액세스를 제어 합니다.

내보내기 규칙을 사용 하는 경우 캐시는 캐시 서브넷의 여러 IP 주소를 사용할 수 있습니다. 모든 가능한 서브넷 IP 주소 범위에서 액세스를 허용 합니다.

> [!NOTE]
> 기본적으로 Azure HPC 캐시는 squashes 루트 액세스를 사용 합니다. 자세한 내용은 [추가 캐시 설정 구성](configuration.md#configure-root-squash) 을 참조 하세요.

NAS 저장소 공급 업체와 협력 하 여 캐시에 대 한 적절 한 수준의 액세스를 사용 하도록 설정 합니다.

### <a name="allow-root-access-on-directory-paths"></a>디렉터리 경로에 대 한 루트 액세스 허용
<!-- linked in prereqs article -->

계층 디렉터리를 내보내는 NAS 시스템의 경우 Azure HPC 캐시에는 각 내보내기 수준에 대 한 루트 액세스 권한이 필요 합니다.

예를 들어 시스템에 다음과 같은 세 가지 내보내기가 표시 될 수 있습니다.

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

내보내기는 ``/ifs/accounting/payroll`` 의 자식이 ``/ifs/accounting`` 고 ``/ifs/accounting`` 자체는의 자식입니다 ``/ifs`` .

``payroll``내보내기를 HPC 캐시 저장소 대상으로 추가 하는 경우 캐시는 실제로 ``/ifs/`` 여기에서 급여 디렉터리를 탑재 하 고 액세스 합니다. 따라서 Azure HPC 캐시는 내보내기에 액세스 하기 위해에 대 한 루트 액세스가 필요 ``/ifs`` ``/ifs/accounting/payroll`` 합니다.

이 요구 사항은 저장소 시스템에서 제공 하는 파일 핸들을 사용 하 여 캐시에서 파일을 인덱싱하고 파일 충돌을 방지 하는 방법과 관련이 있습니다.

계층적 내보내기를 사용 하는 NAS 시스템은 파일이 다른 내보내기에서 검색 되는 경우 동일한 파일에 대해 서로 다른 파일 핸들을 제공할 수 있습니다. 예를 들어 클라이언트가 파일을 탑재 ``/ifs/accounting`` 하 고 액세스할 수 있습니다 ``payroll/2011.txt`` . 다른 클라이언트 ``/ifs/accounting/payroll`` 는 파일을 탑재 하 고 액세스 ``2011.txt`` 합니다. 저장소 시스템에서 파일 핸들을 할당 하는 방법에 따라이 두 클라이언트는 서로 다른 파일 핸들을 사용 하는 동일한 파일을 받을 수 있습니다 ``<mount2>/payroll/2011.txt`` ``<mount3>/2011.txt`` .

백 엔드 저장소 시스템은 파일 핸들에 대 한 내부 별칭을 유지 하지만 Azure HPC 캐시는 동일한 항목을 참조 하는 인덱스의 파일 핸들을 알 수 없습니다. 따라서 캐시가 동일한 파일에 대해 캐시 된 쓰기를 여러 개 가질 수 있으며, 동일한 파일 임을 알지 못하기 때문에 변경 내용을 잘못 적용할 수 있습니다.

여러 내보내기의 파일에 대해 이러한 가능한 파일 충돌을 방지 하기 위해 Azure HPC 캐시는 shallowest 사용 가능한 내보내기를 경로에 자동으로 탑재 하 ``/ifs`` 고 (예제에서는) 해당 내보내기에서 제공 하는 파일 핸들을 사용 합니다. 여러 내보내기에서 동일한 기본 경로를 사용 하는 경우 Azure HPC 캐시에는 해당 경로에 대 한 루트 액세스 권한이 필요 합니다.

## <a name="enable-export-listing"></a>내보내기 목록 사용
<!-- link in prereqs article -->

Azure HPC 캐시가 쿼리를 쿼리할 때 NAS는 해당 내보내기를 나열 해야 합니다.

대부분의 NFS 저장소 시스템에서 Linux 클라이언트에서 다음 쿼리를 전송 하 여 테스트할 수 있습니다.``showmount -e <storage IP address>``

가능 하면 캐시와 동일한 가상 네트워크의 Linux 클라이언트를 사용 합니다.

해당 명령으로 내보내기가 나열 되지 않으면 캐시에서 저장소 시스템에 연결 하는 데 문제가 있는 것입니다. NAS 공급 업체와 협력 하 여 내보내기 목록을 사용 하도록 설정 합니다.

## <a name="adjust-vpn-packet-size-restrictions"></a>VPN 패킷 크기 제한 조정
<!-- link in prereqs article and configuration article -->

캐시와 NAS 장치 간의 VPN이 있는 경우 VPN은 전체 크기의 1500 바이트 이더넷 패킷을 차단할 수 있습니다. NAS와 Azure HPC 캐시 인스턴스 간의 대량 교환이 완료 되지 않지만 더 작은 업데이트가 예상 대로 작동 하는 경우이 문제가 발생할 수 있습니다.

VPN 구성의 세부 정보를 알 수 없는 경우 시스템에이 문제가 있는지 여부를 알 수 있는 간단한 방법은 없습니다. 다음은이 문제를 확인 하는 데 도움이 될 수 있는 몇 가지 방법입니다.

* VPN의 양쪽에서 패킷 스니퍼를 사용 하 여 성공적으로 전송 되는 패킷을 검색 합니다.
* VPN에서 ping 명령을 허용 하는 경우 전체 크기 패킷 전송을 테스트할 수 있습니다.

  이러한 옵션을 사용 하 여 VPN을 통해 NAS에 대해 ping 명령을 실행 합니다. *<storage_IP>* 값 대신 저장소 시스템의 IP 주소를 사용 합니다.

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  명령의 옵션은 다음과 같습니다.

  * ``-M do``-단편화 안 함
  * ``-c 1``-패킷을 하나만 보냅니다.
  * ``-s 1472``-페이로드의 크기를 1472 바이트로 설정 합니다. 이더넷 오버 헤드를 고려한 후 1500 바이트 패킷에 대 한 최대 크기 페이로드입니다.

  성공적인 응답은 다음과 같습니다.

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Ping이 1472 바이트와 함께 실패 하는 경우 패킷 크기 문제가 있을 수 있습니다.

문제를 해결 하려면 원격 시스템이 최대 프레임 크기를 올바르게 검색 하도록 VPN에서 MSS 고정을 구성 해야 할 수 있습니다. 자세한 내용은 [IPsec/IKE 매개 변수 VPN Gateway 설명서](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) 를 참조 하세요.

경우에 따라 Azure HPC 캐시의 MTU 설정을 1400로 변경 하면 도움이 될 수 있습니다. 그러나 캐시에서 MTU를 제한 하는 경우에는 캐시와 상호 작용 하는 백 엔드 저장소 시스템 및 클라이언트에 대 한 MTU 설정도 제한 해야 합니다. 자세한 내용은 [추가 AZURE HPC 캐시 설정 구성](configuration.md#adjust-mtu-value) 을 참조 하세요.

## <a name="check-for-acl-security-style"></a>ACL 보안 스타일 확인

일부 NAS 시스템은 Acl (액세스 제어 목록)을 기존 POSIX 또는 UNIX 보안과 결합 하는 하이브리드 보안 스타일을 사용 합니다.

시스템에서 "ACL" 머리글자어를 포함 하지 않고 보안 스타일을 UNIX 또는 POSIX로 보고 하는 경우이 문제는 영향을 받지 않습니다.

Acl을 사용 하는 시스템의 경우, Azure HPC 캐시는 파일 액세스를 제어 하기 위해 사용자 관련 추가 값을 추적 해야 합니다. 이 작업은 액세스 캐시를 사용 하도록 설정 하 여 수행 합니다. 액세스 캐시를 켜는 사용자 측 컨트롤은 없지만 지원 티켓을 열어 캐시 시스템의 영향을 받는 저장소 대상에 대해 사용 하도록 설정 하도록 요청할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 해결 되지 않은 문제가 있는 경우 [지원 티켓을 열어](hpc-cache-support-ticket.md) 전문가의 도움을 받으세요.
