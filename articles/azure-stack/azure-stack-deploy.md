---
title: "Azure 스택 개발 키트 배포 필수 구성 요소 | Microsoft Docs"
description: "Azure 스택 개발 키트 (클라우드 연산자)에 대 한 환경 및 하드웨어 요구 사항을 봅니다."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 32a21d9b-ee42-417d-8e54-98a7f90f7311
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/08/2017
ms.author: jeffgilb
ms.openlocfilehash: 2410b1a3c3c2856cceda87ced7e66f140ea0c323
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2018
---
# <a name="azure-stack-deployment-prerequisites"></a>Azure 스택 배포 필수 구성 요소

*적용 대상: Azure 스택 개발 키트*

배포 하기 전에 [Azure 스택 개발 키트](azure-stack-poc.md), 컴퓨터가 다음 요구 사항을 충족 하는지 확인 하십시오.


## <a name="hardware"></a>하드웨어
| 구성 요소 | 최소 | 권장 |
| --- | --- | --- |
| 디스크 드라이브: 운영 체제 |시스템 파티션에 사용할 수 있는 최소 200GB의 운영 체제 디스크 1개(SSD 또는 HDD) |시스템 파티션에 사용할 수 있는 최소 200GB의 운영 체제 디스크 1개(SSD 또는 HDD) |
| 디스크 드라이브: 일반 개발 키트 데이터 * |4개의 디스크. 각 디스크는 최소 140GB의 용량을 제공합니다(SSD 또는 HDD). 사용 가능한 모든 디스크 사용 됩니다. |4개의 디스크. 각 디스크 용량 (SSD 또는 HDD) 250GB 최소를 제공합니다. 사용 가능한 모든 디스크 사용 됩니다. |
| Compute: CPU |듀얼 소켓의 경우: 12 실제 코어 수 (합계) |듀얼 소켓의 경우: 16 실제 코어 수 (합계) |
| Compute: 메모리 |96GB RAM |128GB RAM (PaaS 리소스 공급자를 지원 하기 위해 최소입니다.)|
| Compute: BIOS |Hyper-V 사용(SLAT 지원) |Hyper-V 사용(SLAT 지원) |
| 네트워크: NIC |NIC에 필요한 Windows Server 2012 R2 인증; 특수 기능 필요 없음 |NIC에 필요한 Windows Server 2012 R2 인증; 특수 기능 필요 없음 |
| HW 로고 인증 |[Windows Server 2012 R2에 대한 인증](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Windows Server 2012 R2에 대한 인증](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

\*다양 한를 추가 하려는 경우이 권장 용량 보다 더 많은 해야는 [마켓플레이스 항목](azure-stack-download-azure-marketplace-item.md) Azure에서.

**데이터 디스크 드라이브 구성:** 모든 데이터 드라이브는 동일한 유형 (모든 SAS, 모든 SATA 또는 모든 NVMe) 및 용량 이어야 합니다. SAS 디스크 드라이브를 사용 하면 단일 경로 (다중 경로 지원이 없는 MPIO 제공)를 통해 디스크 드라이브를 첨부 되어야 합니다.

**HBA 구성 옵션**

* (기본 설정) 간단한 HBA
* RAID HBA – 어댑터 "통과" 모드로 구성 해야 합니다.
* RAID HBA – 디스크는 단일 디스크, RAID-0으로 구성되어야 합니다.

**지원되는 버스 및 미디어 형식 조합**

* SATA HDD
* SAS HDD
* RAID HDD
* RAID SSD (미디어 형식이 지정 되지 않은/알 수 없는 경우\*)
* SATA SSD + SATA HDD
* SAS SSD + SAS HDD
* NVMe

\* 통과 기능 없이 RAID 컨트롤러에는 미디어 유형을 인식할 수 없습니다. 이러한 컨트롤러는 지정되지 않음으로 SSD와 HDD를 표시합니다. 이 경우 SSD는 캐싱 장치 대신 영구적 저장소로 사용됩니다. 따라서 이러한 ssd 개발 키트를 배포할 수 있습니다.

**예제 HBA**: LSI 9207 8i, LSI-9300-8i 또는 LSI-9265-8i 통과 모드

샘플 OEM 구성을 사용할 수 있습니다.

## <a name="operating-system"></a>운영 체제
|  | **요구 사항** |
| --- | --- |
| **OS 버전** |Windows Server 2012 R2 이상. 운영 체제 버전은 중요 한 배포를 시작 하기 전에으로 Azure 스택 설치에 포함 된 VHD에 호스트 컴퓨터를 부팅 합니다. 모든 필요한 패치 및 운영 체제 이미지에 이미 통합 됩니다. 개발 키트에서 사용 되는 모든 Windows Server 인스턴스를 활성화 하는 모든 키를 사용 하지 마십시오. |

## <a name="deployment-requirements-check-tool"></a>배포 요구 사항 확인 도구
운영 체제를 설치한 후 사용할 수 있습니다는 [Azure 스택에 대 한 배포 검사](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) 하드웨어 요구 사항을 모두 충족 하는지 확인 합니다.

## <a name="account-requirements"></a>계정 요구 사항
일반적으로 인터넷 연결 되 면 Microsoft Azure에 연결할 수 있는 개발 키트를 배포 합니다. 이 경우 개발 키트를 배포 하는 Azure Active Directory (Azure AD) 계정을 구성 해야 합니다.

사용자 환경에서 인터넷에 연결 되지 않은 경우 Azure AD를 사용 하지 않음, Active Directory Federation Services (AD FS)을 사용 하 여 Azure 스택을 배포할 수 있습니다. 개발 키트에는 AD FS 및 Active Directory 도메인 서비스 인스턴스는 자체 포함 되어 있습니다. 이 옵션을 사용 하 여 배포 하는 경우에 미리 계정을 설정할 필요가 없습니다.

>[!NOTE]
AD FS 옵션을 사용 하 여 배포 하는 경우에 Azure AD로 전환 하려면 Azure 스택을 다시 배포 해야 합니다.

### <a name="azure-active-directory-accounts"></a>Azure Active Directory 계정
Azure 스택을 Azure AD 계정을 사용 하 여 배포 하려면 배포 PowerShell 스크립트를 실행 하기 전에 Azure AD 계정을 준비 해야 합니다. 이 계정이 Azure AD 테 넌 트에 대 한 전역 관리자가 됩니다. 프로 비전 하 고 응용 프로그램 및 Azure Active Directory Graph API와 상호 작용 하는 모든 Azure 스택을 서비스에 대 한 서비스 사용자를 위임 사용 됩니다. (나중에 변경할 수 있습니다)이 표시 되는 기본 공급자 구독의 소유자도도 사용 됩니다. 이 계정을 사용 하 여 Azure 스택 시스템의 관리자 포털에 로그인 수 있습니다.

1. 하나 이상의 Azure AD에 대 한 디렉터리 관리자가 해당 하는 Azure AD 계정을 만듭니다. 를 이미 있는 하나를 사용할 수 있습니다. 그렇지 않으면에서 만들 수 있습니다 하나를 무료로 [http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (중국 방문 <http://go.microsoft.com/fwlink/?LinkID=717821> 대신). 하려는 경우 나중에 [Azure를 사용한 Azure 스택 등록](azure-stack-register.md), 구독을 새로 만든 계정에도 있어야 합니다.
   
    6 단계에서 사용 하기 위해이 자격 증명을 저장 [개발 키트 배포](azure-stack-run-powershell-script.md)합니다. 이 *서비스 관리자* 계정을 구성 하 고 리소스 클라우드, 사용자 계정, 테 넌 트 계획, 할당량 및 가격을 관리할 수 있습니다. 포털에서 웹 사이트 클라우드, 가상 컴퓨터 개인 클라우드 및 계획을 만들고 사용자 구독을 관리할 수 있습니다.
2. [만들](azure-stack-add-new-user-aad.md) 테 넌 트로 개발 키트에 로그인 할 수 있도록 계정 하나 이상 있습니다.
   
   | **Azure Active Directory 계정** | **지원됩니까?** |
   | --- | --- |
   | 유효한 공용 Azure 구독에 회사 또는 학교 계정 |예 |
   | 유효한 공용 Azure 구독의 Microsoft 계정 |예 |
   | 회사 또는 학교 계정을 유효한 중국 Azure 구독 |예 |
   | 유효한 미국 정부 Azure 구독에 회사 또는 학교 계정 |예 |

## <a name="network"></a>네트워크
### <a name="switch"></a>Switch
개발 키트 컴퓨터에 대 한 스위치에서 사용 가능한 포트를 하나의 합니다.  

개발 키트 컴퓨터 스위치 액세스 포트 또는 트렁크 포트에 연결을 지원 합니다. 스위치에 특수한 기능이 필요하지 않습니다. 트렁크 포트를 사용하거나 VLAN ID를 구성해야 하는 경우 배포 매개 변수로 VLAN ID를 제공해야 합니다. 예제를 확인할 수는 [배포 매개 변수 목록이](azure-stack-run-powershell-script.md)합니다.

### <a name="subnet"></a>서브넷
서브넷에 개발 키트 컴퓨터를 연결 하지 않습니다.

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

이러한 서브넷은 개발 키트 환경 내에서 내부 네트워크에 예약 되어 있습니다.

### <a name="ipv4ipv6"></a>IPv4/IPv6
IPv4만 지원됩니다. IPv6 네트워크를 만들 수 없습니다.

### <a name="dhcp"></a>DHCP
NIC가 연결하는 네트워크에서 사용 가능한 DHCP 서버가 있는지 확인합니다. DHCP를 사용할 수 없는 경우 호스트에서 사용하는 것 외에도 추가 정적 IPv4 네트워크를 준비해야 합니다. 해당 IP 주소를 제공 해야 및 게이트웨이 배포 매개 변수입니다. 예제를 확인할 수는 [배포 매개 변수 목록이](azure-stack-run-powershell-script.md)합니다.

### <a name="internet-access"></a>인터넷 액세스
Azure 스택 직접 또는 투명 프록시를 통해 인터넷 액세스를 해야합니다. Azure 스택 인터넷 액세스를 사용 하는 웹 프록시 구성을 지원 하지 않습니다. 호스트 IP 및 DHCP 또는 고정 IP) (여 MA BGPNAT01에 할당 된 새 IP를 인터넷에 액세스할 수 있어야 합니다. 포트 80 및 443 graph.windows.net 및 login.microsoftonline.com 도메인에서 사용 됩니다.

Q:는 ASDK Azure VM에서 실행할 수 있습니까? A: 아니요. 현재 시점에서 공식적으로 없습니다.

## <a name="next-steps"></a>다음 단계
[Azure 스택 개발 키트 배포 패키지를 다운로드 합니다.](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Azure 스택 개발 키트를 배포 합니다.](azure-stack-run-powershell-script.md)
