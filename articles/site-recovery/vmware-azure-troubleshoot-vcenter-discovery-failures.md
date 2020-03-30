---
title: Azure 사이트 복구에서 VMware vCenter 검색 실패 해결
description: 이 문서에서는 Azure 사이트 복구에서 VMware vCenter 검색 실패 문제를 해결하는 방법을 설명합니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: f00c7b12accde9df9a5708a2b8b378d70428318d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74091247"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>vCenter 서버 검색 오류 해결

이 문서에서는 VMware vCenter 검색 실패로 인해 발생하는 문제를 해결하는 데 도움이 됩니다.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>maxSnapShots 속성의 비숫자 값

9.20 이전 버전에서 vCenter는 VM에서 속성에 `snapshot.maxSnapShots` 대한 비숫자 값을 검색할 때 연결이 끊어집니다.

이 문제는 오류 ID 95126으로 식별됩니다.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
이 문제를 해결하려면

- VM을 식별하고 값을 숫자 값(vCenter의 VM 편집 설정)으로 설정합니다.

또는

- 구성 서버를 버전 9.20 이상으로 업그레이드합니다.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>vCenter 연결에 대한 프록시 구성 문제

vCenter 검색은 시스템 사용자가 구성한 시스템 기본 프록시 설정을 준수합니다. DRA 서비스는 통합 설치 관리자 또는 OVA 템플릿을 사용하여 구성 서버를 설치하는 동안 사용자가 제공하는 프록시 설정을 준수합니다. 

일반적으로 프록시는 공용 네트워크와 통신하는 데 사용됩니다. Azure와 통신하는 것과 같은 프록시가 구성되고 vCenter가 로컬 환경에 있는 경우 DRA와 통신할 수 없습니다.

이 문제가 발생할 때 다음과 같은 상황이 발생합니다.

- vCenter 서버 \<vCenter> 오류로 인해 연결할 수 없습니다: 원격 서버가 오류를 반환했습니다: (503) 서버를 사용할 수 없음
- vCenter 서버 \<vCenter> 오류로 인해 연결할 수 없습니다: 원격 서버가 오류를 반환: 원격 서버에 연결할 수 없습니다.
- vCenter/ESXi 서버에 연결할 수 없습니다.

이 문제를 해결하려면

[PsExec 도구를 다운로드하십시오.](https://aka.ms/PsExec) 

PsExec 도구를 사용하여 시스템 사용자 컨텍스트에 액세스하고 프록시 주소가 구성되었는지 확인합니다. 그런 다음 다음 절차를 사용하여 vCenter를 바이패스 목록에 추가할 수 있습니다.

검색 프록시 구성의 경우:

1. PsExec 도구를 사용하여 시스템 사용자 컨텍스트에서 IE를 엽니다.
    
    psexec -s -i "%programfiles%\인터넷 익스플로러\iexplore.exe"

2. 인터넷 익스플로러의 프록시 설정을 수정하여 vCenter IP 주소를 우회합니다.
3. tmanssvc 서비스를 다시 시작합니다.

DRA 프록시 구성의 경우:

1. 명령 프롬프트를 열고 Microsoft Azure 사이트 복구 공급자 폴더를 엽니다.
 
    **cd C:\프로그램 파일\마이크로소프트 Azure 사이트 복구 공급자**

3. 명령 프롬프트에서 다음 명령을 실행합니다.
   
   **DRCONFIGURATIONATOR. EXE /configure /AddBypassUrls [vCenter 추가 시 제공된 vCenter 서버의 IP 주소/FQDN]**

4. DRA 공급자 서비스를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

[VMware VM 재해 복구용 구성 서버 관리](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
