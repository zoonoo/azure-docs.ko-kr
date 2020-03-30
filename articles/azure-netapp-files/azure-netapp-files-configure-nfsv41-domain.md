---
title: Azure NetApp 파일에 대한 NFSv4.1 기본 도메인 구성 | 마이크로 소프트 문서
description: Azure NetApp 파일에서 NFSv4.1을 사용하기 위해 NFS 클라이언트를 구성하는 방법을 설명합니다.
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 77178a23206eadae941794c92b8dd99fe2ca1e05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906287"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Azure NetApp Files에 대한 NFSv 4.1 기본 도메인 구성

NFSv4는 인증 도메인의 개념을 소개합니다. Azure NetApp 파일은 현재 서비스에서 NFS 클라이언트로의 루트 전용 사용자 매핑을 지원합니다. Azure NetApp 파일에서 NFSv4.1 기능을 사용하려면 NFS 클라이언트를 업데이트해야 합니다.

## <a name="default-behavior-of-usergroup-mapping"></a>사용자/그룹 매핑의 기본 동작

NFSv4 `nobody` 도메인이 로 설정되어 있으므로 루트 매핑은 사용자에게 기본값입니다. `localdomain` Azure NetApp 파일 NFSv4.1 볼륨을 루트로 탑재하면 다음과 같이 파일 사용 권한이 표시됩니다.  

![NFSv4.1에 대한 사용자/그룹 매핑의 기본 동작](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

위의 예에서 볼 수 `file1` 있듯이 `root`에 대 한 `nobody` 사용자 이어야 합니다.하지만 기본적으로 매핑 됩니다.  이 문서에서는 `file1` 사용자를 `root`로 설정하는 방법을 보여 주며.  

## <a name="steps"></a>단계 

1. NFS `/etc/idmapd.conf` 클라이언트에서 파일을 편집합니다.   
    줄의 `#Domain` 주석을 해제하고(즉, 줄에서 `#` 제거) `localdomain` 값을 `defaultv4iddomain.com`로 변경합니다. 

    초기 구성: 
    
    ![NFSv4.1에 대한 초기 구성](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    업데이트된 구성:
    
    ![NFSv4.1에 대한 구성업데이트](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. 현재 탑재된 NFS 볼륨의 마운트를 해제합니다.
3. 파일을 `/etc/idmapd.conf` 업데이트합니다.
4. 호스트()에서 `rpcbind` `service rpcbind restart`서비스를 다시 시작하거나 호스트를 재부팅하기만 하면 됩니다.
5. 필요에 따라 NFS 볼륨을 마운트합니다.   

    [Windows 또는 Linux 가상 컴퓨터용 볼륨 마운트 또는 마운트 해제를](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)참조하십시오. 

다음 예제에서는 결과 사용자/그룹 변경 을 보여 주며 있습니다. 

![NFSv4.1에 대한 결과 구성](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

예제에서 볼 수 있듯이 사용자/그룹은 `nobody` 이제 `root`에서 로 변경되었습니다.

## <a name="behavior-of-other-non-root-users-and-groups"></a>다른(루트가 아닌) 사용자 및 그룹의 동작

Azure NetApp 파일은 NFSv4.1 볼륨의 파일 또는 폴더와 연결된 권한이 있는 로컬 사용자(호스트에서 로컬로 만든 사용자)를 지원합니다. 그러나 서비스는 현재 여러 노드에서 사용자/그룹 매핑을 지원하지 않습니다. 따라서 한 호스트에서 만든 사용자는 기본적으로 다른 호스트에서 만든 사용자에게 매핑되지 않습니다. 

다음 예제에서는 `Host1` 세 개의 기존 테스트`testuser01` `testuser02`사용자 `testuser03`계정 (, " 

![NFSv4.1에 대한 결과 구성](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

에서 `Host2`테스트 사용자 계정이 만들어지지 않았지만 두 호스트에 동일한 볼륨이 탑재됩니다.

![NFSv4.1에 대한 결과 구성](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>다음 단계 

[Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 탑재 해제](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

