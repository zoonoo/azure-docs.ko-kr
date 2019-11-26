---
title: Azure Migrate를 사용 하 여 물리적 서버 평가/마이그레이션 지원
description: Azure Migrate를 사용 하 여 물리적 서버 평가/마이그레이션에 대 한 지원을 요약 합니다.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: b5b5da6282b1df6c70fd58dcf8c417250de81b73
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196346"
---
# <a name="support-matrix-for-physical-server-assessment-and-migration"></a>물리적 서버 평가 및 마이그레이션을 위한 지원 매트릭스

[Azure Migrate 서비스](migrate-overview.md) 를 사용 하 여 컴퓨터를 평가 하 고 Microsoft Azure 클라우드로 마이그레이션할 수 있습니다. 이 문서에서는 온-프레미스 물리적 서버를 평가 하 고 마이그레이션하기 위한 지원 설정 및 제한 사항을 요약 합니다.



## <a name="physical-server-scenarios"></a>물리적 서버 시나리오

이 표에는 물리적 서버에 대해 지원 되는 시나리오가 요약 되어 있습니다.

**배포웹사이트를** | **세부 정보***
--- | ---
**온-프레미스 물리적 서버 평가** | 첫 번째 평가를 [설정](tutorial-prepare-physical.md) 합니다.<br/><br/> 평가를 [실행](tutorial-assess-physical.md) 합니다.
**Azure에 물리적 서버 마이그레이션** | Azure로의 마이그레이션을 [시도](tutorial-migrate-physical-virtual-machines.md) 합니다.


## <a name="azure-migrate-projects"></a>Azure Migrate 프로젝트

**지원** | **세부 정보**
--- | ---
**Azure 사용 권한** | Azure Migrate 프로젝트를 만들려면 구독에 대 한 참가자 또는 소유자 권한이 있어야 합니다.
**물리적 서버** | 단일 프로젝트에서 최대 35000 대의 물리적 서버를 평가 합니다. Azure 구독에 여러 프로젝트를 포함할 수 있습니다. 프로젝트는 평가 제한까지 물리적 서버, VMware Vm 및 Hyper-v Vm을 포함할 수 있습니다.
**지리** | 여러 지역에서 Azure Migrate 프로젝트를 만들 수 있습니다. 특정 지역에 프로젝트를 만들 수 있지만 다른 대상 위치의 컴퓨터를 평가 하거나 마이그레이션할 수 있습니다. 프로젝트 지역은 검색된 메타데이터를 저장하는 데만 사용됩니다.

  **지리** | **메타데이터 스토리지 위치**
  --- | ---
  Azure Government | 미국 정부 버지니아
  아시아 태평양 | 동아시아 또는 동남 아시아
  오스트레일리아 | 오스트레일리아 동부 또는 오스트레일리아 남동쪽
  브라질 | 브라질 남부
  캐나다 | 캐나다 중부 또는 캐나다 동부
  유럽 | 북유럽 또는 유럽 서부
  프랑스 | 프랑스 중부
  인도 | 인도 중부 또는 인도 남부
  일본 |  일본 동부 또는 일본 서 부
  한국 | 대한민국 중부 또는 한국 남부
  영국 | 영국 남부 또는 영국 서부
  미국 | 미국 중부 또는 미국 서 부 2


 > [!NOTE]
 > Azure Government에 대 한 지원은 현재 [이전 버전](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) 의 Azure Migrate 에서만 사용할 수 있습니다.


## <a name="assessment-physical-server-requirements"></a>평가-물리적 서버 요구 사항

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **물리적 서버 배포**       | 물리적 서버는 독립 실행형 이거나 클러스터에 배포할 수 있습니다. |
| **권한**           | **Windows:** 검색에 포함 하려는 모든 Windows 서버에서 로컬 사용자 계정을 설정 합니다. 사용자 계정은 원격 데스크톱 사용자, 성능 모니터 사용자 및 성능 로그 사용자 그룹에 추가 해야 합니다. <br/> **Linux:** 검색 하려는 Linux 서버에 루트 계정이 필요 합니다. |
| **운영 체제** | 모든 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 및 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) 운영 체제는 다음을 제외 하 고 지원 됩니다.<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="assessment-appliance-requirements"></a>평가-어플라이언스 요구 사항

평가를 위해 Azure Migrate 경량 어플라이언스를 실행 하 여 물리적 서버를 검색 하 고 서버 메타 데이터 및 성능 데이터를 Azure Migrate 보냅니다. 어플라이언스는 물리적 서버 또는 VM에서 실행할 수 있으며, Azure Portal에서 다운로드 하는 PowerShell 스크립트를 사용 하 여 설정할 수 있습니다. 다음 표에는 어플라이언스 요구 사항이 요약 되어 있습니다.

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **어플라이언스 배포**   |  어플라이언스는 물리적 서버 또는 가상 머신에 배포 합니다.<br/>  호스트 컴퓨터에서 Windows Server 2012 R2 이상을 실행 해야 합니다.<br/> 호스트는 16gb RAM, 8 개 vCPUs, 80 GB의 저장소 공간 및 어플라이언스 VM에 대 한 외부 스위치를 할당 하는 데 충분 한 공간이 필요 합니다.<br/> 어플라이언스에는 정적 또는 동적 IP 주소와 인터넷 액세스가 필요 합니다.
| **Azure Migrate 프로젝트**  |  어플라이언스는 단일 프로젝트에 연결할 수 있습니다.<br/> 모든 수의 어플라이언스를 단일 프로젝트에 연결할 수 있습니다.<br/> 프로젝트에서 최대 35000 대의 컴퓨터를 평가할 수 있습니다.
| **조사**              | 단일 어플라이언스에서 최대 250 서버를 검색할 수 있습니다.
| **평가 그룹**       | 단일 그룹에 최대 35000 대의 컴퓨터를 추가할 수 있습니다.
| **평가**             | 단일 평가에서 최대 35000 대의 컴퓨터를 평가할 수 있습니다.


## <a name="assessment-appliance-url-access"></a>평가-어플라이언스 URL 액세스

Vm을 평가 하려면 Azure Migrate 어플라이언스에서 인터넷에 연결 해야 합니다.

- 어플라이언스를 배포 하는 경우 Azure Migrate 아래 표에 요약 된 Url에 대 한 연결 확인을 수행 합니다.
- URL 기반 프록시를 사용 하는 경우 테이블의 Url에 대 한 액세스를 허용 하 여 프록시가 Url을 조회 하는 동안 수신 된 CNAME 레코드를 확인 하는지 확인 합니다.
- 가로채기 프록시가 있는 경우 프록시 서버에서 어플라이언스로 서버 인증서를 가져와야 할 수 있습니다.


**URL** | **세부 정보**  
--- | ---
*.portal.azure.com | Azure Portal 탐색
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Azure 구독에 로그인합니다.
*.microsoftonline.com <br/> *.microsoftonline-p.com | 어플라이언스에서 서비스 통신에 대 한 Azure Active Directory 응용 프로그램 만들기
management.azure.com | 어플라이언스에서 서비스 통신에 대 한 Azure Active Directory 응용 프로그램 만들기
dc.services.visualstudio.com | 로깅 및 모니터링
*.vault.azure.net | 어플라이언스와 서비스 간에 통신 하는 경우 Azure Key Vault의 비밀을 관리 합니다.
aka.ms/* | 즉, 링크에 대 한 액세스를 허용 합니다.
https://download.microsoft.com/download/* | Microsoft 다운로드 사이트에서 다운로드할 수 있습니다.



## <a name="assessment-port-requirements"></a>평가-포트 요구 사항

다음 표에는 평가를 위한 포트 요구 사항이 요약 되어 있습니다.

**디바이스** | **연결**
--- | ---
**기기가** | 어플라이언스에 대 한 원격 데스크톱 연결을 허용 하기 위해 TCP 포트 3389에서 인바운드 연결<br/> URL을 사용 하 여 어플라이언스 관리 앱에 원격으로 액세스 하기 위한 포트 44368의 인바운드 연결: ``` https://<appliance-ip-or-name>:44368 ```<br/> Azure Migrate에 검색 및 성능 메타 데이터를 보내기 위한 포트 443, 5671 및 5672의 아웃 바운드 연결
**물리적 서버** | **Windows:** Windows 서버에서 끌어오기 구성 및 성능 메타 데이터에 대 한 포트 443, 5989에서 인바운드 연결 <br/> **Linux:**  Linux 서버에서 구성 및 성능 메타 데이터를 가져오기 위해 포트 22 (UDP)에서 인바운드 연결 |


## <a name="next-steps"></a>다음 단계

물리적 서버 평가 및 마이그레이션에 대 한 [물리적 서버 평가를 준비](tutorial-prepare-physical.md) 합니다.
