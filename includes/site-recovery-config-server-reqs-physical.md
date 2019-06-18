---
title: 포함 파일
description: 포함 파일
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: afeae4af9b41bf434b26833a3bd927118a4697ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160362"
---
**물리적 서버 복제에 대한 구성/프로세스 서버 요구 사항**

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
IP 주소 유형 | 공용 
| 
**액세스 설정** | 
MYSQL | MySQL은 구성 서버에 설치해야 합니다. 수동으로 설치할 수도 있고 Site Recovery를 통해 배포하는 동안 설치할 수도 있습니다. Site Recovery가 설치하도록 하려면 머신이 http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi 에 도달할 수 있는지 확인합니다.
URL | 구성 서버가 이러한 URL에 액세스해야 합니다(직접 또는 프록시를 통해).<br/><br/> Azure AD: `login.microsoftonline.com`; `login.microsoftonline.us`; `*.accesscontrol.windows.net`<br/><br/> 복제 데이터 전송: `*.backup.windowsazure.com`; `*.backup.windowsazure.us`<br/><br/> 복제 관리: `*.hypervrecoverymanager.windowsazure.com`; `*.hypervrecoverymanager.windowsazure.us`; `https://management.azure.com`; `*.services.visualstudio.com`<br/><br/> 저장소 액세스: `*.blob.core.windows.net`; `*.blob.core.usgovcloudapi.net`<br/><br/> 시간 동기화: `time.nist.gov`; `time.windows.com`<br/><br/> (선택 사항) 원격 분석: `dc.services.visualstudio.com`
방화벽 | IP 주소 기반 방화벽 규칙은 Azure URL과의 통신을 허용해야 합니다. IP 범위를 간소화하고 제한하려면 URL 필터링을 사용하는 것이 좋습니다.<br/><br/>**상용 IP의 경우:**<br/><br/>- [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.<br/><br/> - 미국 서부에 해당하는 IP 주소 범위를 허용합니다(액세스 제어 및 ID 관리에 사용됨).<br/><br/> - Azure Active Directory, 백업, 복제 및 저장소에 필요한 URL을 지원하기 위해 구독의 Azure 지역에 대한 IP 주소 범위를 허용합니다.<br/><br/> **정부를 위한 IP의 경우:**<br/><br/> - Azure Government 데이터 센터 IP 범위 및 HTTPS(443) 포트를 허용합니다.<br/><br/> - Azure Active Directory, 백업, 복제 및 저장소에 필요한 URL을 지원하기 위해 모든 US Gov 지역(버지니아, 텍사스, 아리조나 및 아이오와)에 대한 IP 주소 범위를 허용합니다.
포트 | 443 허용(컨트롤 채널 오케스트레이션)<br/><br/> 9443 허용(데이터 전송) 


**구성/프로세스 서버 크기 요구 사항**

**CPU** | **메모리** | **캐시 디스크** | **데이터 변경률** | **복제된 컴퓨터**
--- | --- | --- | --- | ---
vCPU 8대<br/><br/> 2개 소켓 * 4코어 \@ 2.5GHz | 16GB | 300GB | 500GB 이하 | 컴퓨터 100대 미만
vCPU 12대<br/><br/> 2개 소켓 * 6코어 \@ 2.5GHz | 18GB | 600GB | 500GB-1TB | 컴퓨터 100-150대
vCPU 16대<br/><br/> 2개 소켓 * 8코어 \@ 2.5GHz | 32GB | 1TB | 1-2TB | 컴퓨터 150-200대

