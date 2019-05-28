---
title: 포함 파일
description: 포함 파일
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 371cbcc50b574f95e8d9ba4efe79058b2b25a8ba
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127708"
---
**구성/프로세스 서버 요구 사항**

**구성 요소** | **요구 사항** 
--- | ---
**하드웨어 설정** | 
CPU 코어 | 8 
RAM | 16GB
디스크 수 | 3, OS 디스크, 프로세스 서버 캐시 디스크, 보존 드라이브(장애 복구용) 포함 
사용 가능한 디스크 공간(프로세스 서버 캐시) | 600GB
사용 가능한 디스크 공간(보존 디스크) | 600GB
 | 
**소프트웨어 설정** | 
운영 체제 | Windows Server 2012 R2 <br> Windows Server 2016
운영 체제 로케일 | 미국 영어(en-us)
Windows Server 역할 | 다음 역할을 사용하지 않도록 설정함: <br> - Active Directory Domain Services <br>- 인터넷 정보 서비스 <br> - Hyper-V 
그룹 정책 | 다음 그룹 정책을 사용하지 않도록 설정함: <br> - 명령 프롬프트에 대한 액세스 방지 <br> - 레지스트리 편집 도구에 대한 액세스 방지 <br> - 파일 첨부를 위한 트러스트 논리 <br> - 스크립트 실행 켜기 <br> [자세히 알아보기](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 기존의 기본 웹 사이트 없음 <br> - 포트 443에서 수신 대기하는 기존의 웹 사이트/응용 프로그램 없음 <br>- [익명 인증](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) 사용 <br> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 설정 사용 
| 
**네트워크 설정** | 
IP 주소 유형 | 공용 
포트 | 443(컨트롤 채널 오케스트레이션)<br>9443(데이터 전송) 
NIC 유형 | VMXNET3(구성 서버가 VMware VM인 경우)
 |
**인터넷 액세스**(서버에서 다음 URL에 액세스해야 합니다. - 직접 또는 프록시를 통해):|
\*.backup.windowsazure.com | 복제된 데이터 전송 및 조정에 사용됩니다.
\*.store.core.windows.net | 복제된 데이터 전송 및 조정에 사용됩니다.
\*.blob.core.windows.net | 복제된 데이터를 저장하는 스토리지 계정에 액세스하는 데 사용됩니다.
\*.hypervrecoverymanager.windowsazure.com | 복제 관리 작업 및 조정에 사용됩니다.
https:\//management.azure.com | 복제 관리 작업 및 조정에 사용됩니다. 
*.services.visualstudio.com | 원격 분석 용도로 사용(선택 사항)
time.nist.gov | 시스템 시간과 글로벌 시간 사이의 시간 동기화를 확인하는 데 사용됩니다.
time.windows.com | 시스템 시간과 글로벌 시간 사이의 시간 동기화를 확인하는 데 사용됩니다.
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | OVF 설정에서 이러한 URL에 액세스해야 합니다. Azure Active Directory에서 액세스 제어 및 ID 관리에 사용됩니다.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | MySQL 다운로드를 완료하려면
|
**설치할 소프트웨어** | 
VMware vSphere PowerCLI | 구성 서버가 VMware VM에서 실행되는 경우 [PowerCLI 버전 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)을 설치해야 합니다.
MYSQL | MySQL을 설치해야 합니다. 수동으로 설치할 수도 있고 Site Recovery를 통해 설치할 수도 있습니다. (자세한 내용은 [설정 구성](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings)을 참조하세요.)

**구성/프로세스 서버 크기 요구 사항**

**CPU** | **메모리** | **캐시 디스크** | **데이터 변경률** | **복제된 컴퓨터**
--- | --- | --- | --- | ---
vCPU 8대<br/><br/> 2개 소켓 * 4코어 \@ 2.5GHz | 16GB | 300GB | 500GB 이하 | 컴퓨터 100대 미만
vCPU 12대<br/><br/> 2개 소켓 * 6코어 \@ 2.5GHz | 18GB | 600GB | 500GB-1TB | 컴퓨터 100-150대
vCPU 16대<br/><br/> 2개 소켓 * 8코어 \@ 2.5GHz | 32GB | 1TB | 1-2TB | 컴퓨터 150-200대

