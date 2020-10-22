---
title: Azure Site Recovery에서 VMware vCenter 검색 오류 문제 해결
description: 이 문서에서는 Azure Site Recovery에서 VMware vCenter 검색 오류 문제를 해결 하는 방법을 설명 합니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: 1a8471305af93194ccae7b0928685e10d4d64726
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92366662"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>vCenter Server 검색 오류 문제 해결

이 문서는 VMware vCenter 검색 오류로 인해 발생 하는 문제를 해결 하는 데 도움이 됩니다.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>MaxSnapShots 속성의 숫자가 아닌 값

9.20 이전 버전에서는 VM의 property 속성에 대해 숫자가 아닌 값을 검색할 때 vCenter의 연결이 끊깁니다  `snapshot.maxSnapShots` .

이 문제는 오류 ID 95126으로 식별 됩니다.

```output
ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
System.FormatException: Input string was not in a correct format.
    at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
    at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
    at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
```

문제를 해결하려면:

- VM을 식별 하 고 값을 숫자 값 (vCenter의 VM 편집 설정)으로 설정 합니다.

또는

- 구성 서버를 버전 9.20 이상으로 업그레이드 합니다.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>VCenter 연결에 대 한 프록시 구성 문제

vCenter 검색은 시스템 사용자가 구성한 시스템 기본 프록시 설정을 사용 합니다. DRA 서비스는 통합 설치 관리자 또는 OVA 템플릿을 사용 하 여 구성 서버를 설치 하는 동안 사용자가 제공 하는 프록시 설정을 적용 합니다. 

일반적으로 프록시는 공용 네트워크와 통신 하는 데 사용 됩니다. 예: Azure와의 통신. 프록시가 구성 되어 있고 vCenter가 로컬 환경에 있는 경우 DRA와 통신할 수 없습니다.

이 문제가 발생 하는 경우 다음 상황이 발생 합니다.

- 오류가 발생 하 여 vCenter 서버에 \<vCenter> 연결할 수 없습니다. 원격 서버에서 오류를 반환 했습니다. (503) 서버를 사용할 수 없습니다.
- 오류가 발생 하 여 vCenter 서버에 연결할 수 없습니다. 원격 서버에서 오류를 반환 했습니다. \<vCenter> 원격 서버에 연결할 수 없습니다.
- VCenter/ESXi 서버에 연결할 수 없습니다.

문제를 해결하려면:

[PsExec 도구](/sysinternals/downloads/psexec)를 다운로드합니다. 

PsExec 도구를 사용 하 여 시스템 사용자 컨텍스트에 액세스 하 고 프록시 주소가 구성 되었는지 여부를 확인 합니다. 다음 절차를 사용 하 여 바이패스 목록에 vCenter를 추가할 수 있습니다.

검색 프록시 구성의 경우:

1. PsExec 도구를 사용 하 여 시스템 사용자 컨텍스트에서 IE를 엽니다.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Internet Explorer에서 프록시 설정을 수정 하 여 vCenter IP 주소를 무시 합니다.
3. Tmanssvc 서비스를 다시 시작 합니다.

DRA 프록시 구성의 경우:

1. 명령 프롬프트를 열고 Microsoft Azure Site Recovery 공급자 폴더를 엽니다.
 
    **cd C:\Program Files\Microsoft Azure Site Recovery 공급자**

3. 명령 프롬프트에서 다음 명령을 실행 합니다.
   
   **DRCONFIGURATOR.EXE/configure/AddBypassUrls [vCenter 추가 시 제공 된 vCenter Server의 FQDN]**

4. DRA 공급자 서비스를 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

[VMware VM 재해 복구용 구성 서버 관리](./vmware-azure-manage-configuration-server.md#refresh-configuration-server)