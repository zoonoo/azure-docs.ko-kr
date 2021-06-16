---
title: Azure Site Recovery에서 VMware vCenter 검색 오류 문제 해결
description: 이 문서에서는 Azure Site Recovery에서 VMware vCenter 검색 오류 문제를 해결하는 방법을 설명합니다.
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.author: sharrai
ms.date: 05/27/2021
ms.openlocfilehash: 5941539477aa4a4addf39bbf00451e6369ab8415
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110576403"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>vCenter 서버 검색 실패 문제 해결

이 문서는 VMware vCenter 검색 오류로 인해 발생하는 문제를 해결하는 데 도움이 됩니다.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>maxSnapShots 속성의 숫자가 아닌 값

9\.20 이전 버전에서는 VM에서 `snapshot.maxSnapShots` 속성에 대해 숫자가 아닌 값을 검색할 때 vCenter의 연결이 끊깁니다.

이 문제는 오류 ID 95126으로 식별됩니다.

```output
ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
System.FormatException: Input string was not in a correct format.
    at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
    at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
    at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
```

문제를 해결하려면:

- VM을 식별하고 값을 숫자 값으로 설정합니다(vCenter의 VM 편집 설정).

또는

- 구성 서버를 버전 9.20 이상으로 업그레이드합니다.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>vCenter 연결에 대한 프록시 구성 문제

vCenter 검색은 시스템 사용자가 구성한 시스템 기본 프록시 설정을 따릅니다. DRA 서비스는 통합 설치 프로그램 또는 OVA 템플릿을 사용하여 구성 서버를 설치하는 동안 사용자가 제공하는 프록시 설정을 따릅니다. 

일반적으로 프록시는 공용 네트워크와 통신(예: Azure와의 통신)하는 데 사용됩니다. 프록시가 구성되어 있고 vCenter가 로컬 환경에 있는 경우 DRA와 통신할 수 없습니다.

이 문제가 생기면 다음 상황이 발생합니다.

- 다음 오류가 발생하여 vCenter 서버 \<vCenter>에 연결할 수 없습니다. 원격 서버에서 오류: (503) 서버를 사용할 수 없음을 반환했습니다.
- 다음 오류가 발생하여 vCenter 서버 \<vCenter>에 연결할 수 없습니다. 원격 서버에서 오류: 원격 서버에 연결할 수 없음을 반환했습니다.
- vCenter/ESXi 서버에 연결할 수 없습니다.

문제를 해결하려면:

[PsExec 도구](/sysinternals/downloads/psexec)를 다운로드합니다. 

PsExec 도구를 사용하여 시스템 사용자 컨텍스트에 액세스하고 프록시 주소가 구성되었는지 여부를 확인합니다. 다음 절차를 사용하여 바이패스 목록에 vCenter를 추가할 수 있습니다.

검색 프록시 구성의 경우:

1. PsExec 도구를 사용하여 시스템 사용자 컨텍스트에서 IE를 엽니다.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. vCenter IP 주소를 무시하도록 Internet Explorer에서 프록시 설정을 수정합니다.
3. tmanssvc 서비스를 다시 시작합니다.

DRA 프록시 구성의 경우:

1. 명령 프롬프트를 열고 Microsoft Azure Site Recovery Provider 폴더를 엽니다.
 
    **cd C:\Program Files\Microsoft Azure Site Recovery Provider**

3. 명령 프롬프트에서 다음 명령을 실행합니다.
   
   **DRCONFIGURATOR.EXE /configure /AddBypassUrls [vCenter 추가 시 입력한 vCenter Server의 IP 주소/FQDN]**

4. DRA 공급자 서비스를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

[VMware VM 재해 복구용 구성 서버 관리](./vmware-azure-manage-configuration-server.md#refresh-configuration-server)