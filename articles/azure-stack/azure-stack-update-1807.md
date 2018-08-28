---
title: Azure Stack 1807 업데이트 | Microsoft Docs
description: 새로운 1807 업데이트의 알려진된 문제를 포함 하 여 Azure Stack 통합 시스템 및 업데이트를 다운로드 하는 위치에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/27/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: afbe3ff2e6be4e03f8de8ac2490922c3ec788733
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091383"
---
# <a name="azure-stack-1807-update"></a>Azure Stack 1807 업데이트

*적용 대상: Azure Stack 통합 시스템*

이 문서에서는 1807 업데이트 패키지의 내용을 설명 합니다. 이 업데이트는 향상 된 기능, 수정 및 알려진된 문제가 버전의 Azure Stack 및 업데이트를 다운로드 하는 위치를 포함 합니다. 알려진된 문제는 업데이트 프로세스에 직접 관련 된 문제 및 문제 (설치 후) 빌드를 사용 하 여으로 구분 됩니다.

> [!IMPORTANT]  
> Azure Stack 통합 시스템에만이 업데이트 패키지가 됩니다. Azure Stack 개발 키트에는이 업데이트 패키지를 적용 되지 않습니다.

## <a name="build-reference"></a>빌드 참조

Azure Stack 1807 업데이트 빌드 번호는 **1.1807.0.76**합니다.  

### <a name="new-features"></a>새로운 기능

이 업데이트는 Azure Stack에 대 한 다음과 같은 개선 사항이 포함 되어 있습니다.

- <!-- 1658937 | ASDK, IS --> **미리 정의 된 일정에 따라 백업을 시작** -는 어플라이언스로 Azure Stack 이제 자동으로 트리거할 수 사용자의 개입을 제거 하는 정기적으로 백업 인프라입니다. Azure Stack 정의 된 보존 기간 보다 오래 된 백업에 대 한 외부 공유를 자동으로 정리 합니다. 자세한 내용은 [PowerShell 사용 하 여 Azure Stack에 대 한 백업을 사용 하도록 설정](azure-stack-backup-enable-backup-powershell.md)합니다.

- <!-- 2496385 | ASDK, IS -->  **추가 데이터 전송 시간으로 전체 백업 시간입니다.** 자세한 내용은 [PowerShell 사용 하 여 Azure Stack에 대 한 백업을 사용 하도록 설정](azure-stack-backup-enable-backup-powershell.md)합니다.

-   <!-- 1702130 | ASDK, IS -->  **이제 백업 외부 용량 외부 공유의 올바른 용량을 보여 줍니다.** (하드 코드 된이 이전에 10gb입니다.) 자세한 내용은 [PowerShell 사용 하 여 Azure Stack에 대 한 백업을 사용 하도록 설정](azure-stack-backup-enable-backup-powershell.md)합니다.

- <!-- 2508488 |  IS   -->  **용량을 확장할** 하 여 [확장할 단위 노드 추가](azure-stack-add-scale-node.md)합니다.

- <!-- 2753130 |  IS, ASDK   -->  **Azure Resource Manager 템플릿은 이제 condition 요소를 지원** -이제는 조건을 사용 하 여 Azure 리소스 관리자 템플릿에서 리소스를 배포할 수 있습니다. 매개 변수 값이 있는 경우 평가 같은 조건에 따라 리소스를 배포 하도록 템플릿을 디자인할 수 있습니다. 조건으로 서식 파일을 사용 하는 방법에 대 한 정보를 참조 하세요 [조건부 리소스 배포](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) 하 고 [Azure Resource Manager 템플릿의 변수 섹션](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) Azure 설명서에서. 

   템플릿을 사용할 수도 있습니다 [둘 이상의 구독 또는 리소스 그룹에 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment)합니다.  

- <!--2753073 | IS, ASDK -->  **Microsoft.Network API 리소스 버전 지원 고쳤습니다** API 버전 2017-10-01 Azure Stack 네트워크 리소스에 대 한 15 개 2015-06-에 대 한 지원을 포함 하도록 합니다.  리소스 버전 2015-06-15 2017-10-01 사이 대 한 지원이이 릴리스에 포함 되어 있지 않지만 이후 버전에 포함 됩니다.  참조 하십시오 [Azure Stack 네트워킹에 대 한 고려 사항](user/azure-stack-network-differences.md) 기능 차이점에 대 한 합니다.

- <!-- 2272116 | IS, ASDK   -->  **Azure Stack에는 외부 Azure Stack 인프라 끝점에 대 한 역방향 DNS 조회에 대 한 지원이 추가 되었습니다** (즉, adminportal, 관리 포털과 adminmanagement). 이렇게 하면 Azure Stack 외부 끝점 이름을 IP 주소에서 확인할 수 있습니다.

- <!-- 2780899 |  IS, ASDK   --> **Azure Stack은 이제 기존 VM에 추가 네트워크 인터페이스를 추가 지원 합니다.**  이 기능은 포털, PowerShell 및 CLI를 사용 하 여 사용할 수 있습니다. 자세한 내용은 [네트워크 인터페이스 추가 또는 제거](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) Azure 설명서에서. 

- <!-- 2222444 | IS, ASDK   -->  **정확도 및 복원 력 향상 되었습니다 사용량 미터 네트워킹**합니다.  네트워크 사용량 미터 보다 정확 하 게 됩니다 및 일시 중단 하는 계정 구독, 작동 중단 기간 및 경합 상태를 고려 합니다.

- <!-- 2753080 | IS -->  **사용 가능한 알림을 업데이트 합니다.** Azure Stack 배포는 이제 주기적으로 보안 된 끝점을 확인 및 업데이트에 클라우드를 사용할 수 있는지 확인을 연결 합니다. 이 알림을 수동으로 확인 하 고 새 업데이트를 가져온 후와 마찬가지로 업데이트 타일에 표시 됩니다. 에 대해 자세히 알아보세요 [Azure Stack에 대 한 업데이트 관리](azure-stack-updates.md)합니다.

- <!-- 2297790 | IS, ASDK -->  **Azure Stack syslog 클라이언트 (미리 보기 기능)의 향상 된 기능**합니다. 이 클라이언트에 감사 및 Azure Stack 인프라를 syslog 서버 또는 보안 정보 및 이벤트 관리 (SIEM) 소프트웨어의 외부 Azure Stack에 관련 된 로그를 전달할 수 있습니다. Syslog 클라이언트는 이제 일반 텍스트 또는 TLS 1.2 암호화, 후자 되는 기본 구성을 사용 하 여 TCP 프로토콜을 지원 합니다. 서버 전용 또는 상호 인증을 사용 하 여 TLS 연결을 구성할 수 있습니다.

  Syslog 클라이언트 (예: 프로토콜, 암호화 및 인증) syslog 서버와 통신 하는 방법을 구성 하려면 사용 합니다 *집합 SyslogServer* cmdlet. 이 cmdlet은 권한 있는 끝점 (PEP)에서 사용할 수 있습니다.

  Syslog 클라이언트 TLS 1.2 상호 인증에 대 한 클라이언트 쪽 인증서를 추가 하는 PEP에서 집합 SyslogClient cmdlet를 사용 합니다.

  이 미리 보기를 사용 하 여 훨씬 더 많은 감사 및 경고를 볼 수 있습니다. 

  이 기능은 미리 보기 상태에서 이므로 프로덕션 환경에서에 의존 하지 마십시오.

  자세한 내용은 [Azure Stack syslog 전달을](azure-stack-integrate-security.md)합니다.

<!-- - | ####### | IS, ASDK |  **Azure Resource Manager includes the region name.** With this release, objects retrieved from the Azure Resource Manager will now include the region name attribute. If an existing PowerShell script directly passes the object to another cmdlet, the script may produce an error and fail. This is Azure Resource Manager compliant behavior, and requires the calling client to subtract the region attribute. For more information about the Azure Resource Manager see [Azure Resource Manager Documentation](https://docs.microsoft.com/azure/azure-resource-manager/). verify 8-10 mdb -->

- <!-- TBD | IS, ASDK -->  **위임 된 공급자 기능 변경 내용입니다.** Azure 재판매인 모델을 사용 하 여 정렬 위임 된 공급자 모델에 더 잘 간체 1807을 사용 하 여 시작 하 고 위임 된 공급자는 기본적으로 모델을 평면화 하 고 위임 된 공급자를 만드는 다른 위임 된 공급자를 만들 수 없습니다. 단일 수준에서 사용할 수 있는 기능입니다. 새 모델 및 구독 관리로의 전환은 사용 하려면 사용자 구독 동일한 디렉터리 테 넌 트에 속하는 기존 또는 새 위임 된 공급자 구독 간에 이동할 이제 있습니다. 기본 공급자 구독에 속한 사용자 구독을 동일한 디렉터리 테 넌 트 위임 된 공급자 구독에도 이동할 수 있습니다.  자세한 내용은 참조 [Azure Stack에서 제품 위임](azure-stack-delegated-provider.md)합니다.

- <!-- 2536808 IS ASDK --> **VM을 만들 때 향상 된** Azure marketplace에서 다운로드 하는 이미지를 사용 하 여 만든 Vm에 대 한 합니다.

- <!-- TBD | IS, ASDK -->  **Azure Stack Capacity Planner 유용성 개선 사항**합니다. Azure Stack [Capacity Planner](http://aka.ms/azstackcapacityplanner) S2D 캐시 및 S2D 용량 솔루션 Sku를 정의 하는 경우 입력에 대 한 간소화 된 환경을 제공 합니다. 1000 VM 한계로 제한이 제거 되었습니다.


### <a name="fixed-issues"></a>해결된 문제

- <!-- TBD | ASDK, IS --> 다양 한 향상 되었습니다 업데이트 프로세스를 더 신뢰할 수 있도록 합니다. 또한 수정에 적용 된 기본 인프라를 업데이트 하는 동안 작업에 대 한 잠재적인 가동 중지 시간을 최소화 하는 합니다.

- <!--2292271 | ASDK, IS --> 문제를 해결 했습니다 기존 구독에 수정 된 할당량 제한이 적용 되지 않았습니다. 이제 제품의 일부인 네트워크 리소스에 대 한 할당량 한도 시키고 사용자 구독과 연결 된 계획 하는 경우 새로운 제한 값 새 구독 뿐만 아니라 기존 구독에 적용 됩니다.

- <!-- 448955 | IS ASDK --> 이제는 UTC + N 표준 시간대에 배포 된 시스템에 대 한 활동 로그를 성공적으로 쿼리할 수 있습니다.    

- <!-- 2319627 |  ASDK, IS --> 백업 구성 매개 변수 (경로/사용자 이름/암호/암호화 키)에 대 한 사전 검사는 더 이상 백업 구성 설정이 올바르지 않으면를 설정합니다. (이전에 백업 설정이 잘못 설정 된 것과 트리거될 때 백업 실패 후)

- <!-- 2215948 |  ASDK, IS --> 외부 공유에서 백업을 수동으로 삭제할 때에 이제 백업 목록을 새로 고칩니다.

- <!-- 2332636 | IS -->  이 버전으로 업데이트 더 이상 기본 제공 기본 공급자 구독의 기본 소유자를 재설정 **CloudAdmin** 사용자 AD FS를 사용 하 여 배포 하는 경우.

- <!-- 2360715 |  ASDK, IS -->  데이터 센터 통합을 설정한 경우 더 이상 AD FS 메타 데이터 파일은 Azure 파일 공유에서 액세스 합니다. 자세한 내용은 [페더레이션 메타 데이터 파일을 제공 하 여 AD FS 통합 설정](azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file)합니다. 

- <!-- 2388980 | ASDK, IS --> 문제를 해결 했습니다 방지 없도록 할당 기존 공용 IP 주소는 이전에 할당 된 네트워크 인터페이스 또는 부하 분산 장치를 새 네트워크 인터페이스 또는 부하 분산 장치.  

- <!-- 2551834 - IS, ASDK --> 선택 하면 *개요* admin 또는 사용자 포털에서 저장소 계정에는 *Essentials* 창 이제 필요한 모든 정보가 올바르게 표시 합니다. 

- <!-- 2551834 - IS, ASDK --> 선택 하면 *태그* 관리자 또는 사용자 포털에서 저장소 계정에 대 한 정보를 이제 올바르게 표시 됩니다.

- <!-- TBD - IS ASDK --> 이 버전의 Azure Stack OEM 확장 패키지에서 드라이버 업데이트를 적용 하지 못하는 문제를 해결 합니다.

-   <!-- 2055809- IS ASDK --> VM을 만들지 못한 경우 계산 블레이드에서 Vm을 삭제 하지 못하게 하는 문제를 해결 했습니다.  

- <!--  2643962 IS ASDK -->  에 대 한 경고 *메모리 용량을 낮은* 더 이상 잘못 표시 됩니다.

- **다양 한 수정** 성능, 안정성, 보안 및 Azure Stack에서 사용 되는 운영 체제에 대 한 합니다.


<!-- ### Additional releases timed with this update    -->

### <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures
Azure Stack 호스트 키 인프라를 Windows Server 2016의 Server Core 설치를 사용합니다. 이 릴리스에서 Azure Stack에 대 한 인프라 서버에 다음 Windows Server 2016 업데이트를 설치합니다. 
- [CVE-2018-8206](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8206)
- [CVE-2018-8222](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8222)
- [CVE-2018-8282](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8282)
- [CVE-2018-8304](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8304)
- [CVE-2018-8307](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8307)
- [CVE-2018-8308](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8308) 
- [CVE-2018-8309](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8309)
- [CVE-2018-8313](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8313)  

이러한 취약점에 대 한 자세한 내용은 위의 링크를 클릭 하거나 Microsoft 기술 자료 문서를 참조 하세요 [4338814](https://support.microsoft.com/help/4338814) 하 고 [4345418](https://support.microsoft.com/help/4345418)합니다.



## <a name="before-you-begin"></a>시작하기 전에

### <a name="prerequisites"></a>필수 조건

- Azure Stack을 설치 [1805 업데이트](azure-stack-update-1805.md) Azure Stack 1807 업데이트를 적용 하기 전에 합니다.  1806 업데이트가 있었습니다.  

- 사용 가능한 최신 설치 [업데이트 또는 핫픽스 1805 버전용](azure-stack-update-1805.md#post-update-steps)합니다.  
  > [!TIP]  
  > 다음을 구독할 *RRS* 또는 *Atom* Azure Stack 핫픽스를 피드 합니다.
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- 1807 업데이트의 설치를 시작 하기 전에 실행할 [테스트 AzureStack](azure-stack-diagnostic-test.md) 하에 Azure Stack의 상태를 확인 하 고 발견 된 작동 문제를 해결 합니다. 또한 활성 경고를 검토 하 고 작업을 필요로 하는 해결 합니다.

### <a name="known-issues-with-the-update-process"></a>업데이트 프로세스를 사용 하 여 알려진된 문제

- <!-- 2468613 - IS --> 이 업데이트를 설치 하는 동안 경고 제목으로 표시 될 수 있습니다 *오류 – FaultType UserAccounts.New 템플릿을 누락 되었습니다.*  이러한 경고를 안전 하 게 무시할 수 있습니다. 이러한 경고는이 업데이트의 설치가 완료 된 후 자동으로 종료 됩니다.

- <!-- 2489559 - IS --> 이 업데이트를 설치 하는 동안 가상 컴퓨터를 만들 하려고 하지 마십시오. 업데이트를 관리 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Stack 개요에 대 한 업데이트 관리](azure-stack-updates.md#plan-for-updates)합니다.

- <!-- 2830461 - IS --> 특정 상황에서 업데이트에 주의가 필요한 경우 해당 경고 하지 생성 될 수 있습니다. 정확한 상태를 포털에 여전히 반영 됩니다 및 영향을 받지 않습니다.

### <a name="post-update-steps"></a>업데이트 후 단계

- <!-- 2933866 – IS --> **실패 한 업데이트 설치에 대 한 향상 된 상태입니다.** 이 버전에서는 새 상태 범주 두 연산자 실패 한 업데이트 설치에 대 한 자세한 정보를 제공 합니다. 두 범주는 *PreparationFailed*, 및 *InstallationFailed*합니다. 이 버전을 설치한 후 이러한 새 범주를 반영 하도록 수정 된 이전 업데이트 설치 오류에 대 한 정보를 볼 수 있습니다. 

<!-- *There are no post-update steps for update 1807.* -->

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - Link to KB  
 -->

## <a name="known-issues-post-installation"></a>알려진된 문제 (사후 설치)

이 빌드 버전에 대 한 설치 후 알려진된 문제는 다음과 같습니다.

### <a name="portal"></a>포털
- <!-- 2931230 – IS  ASDK --> 사용자 구독에서 해당 계획을 제거 하는 경우에는 추가 요금제는 사용자 구독에 추가 되는 계획을 삭제할 수 없습니다. 계획에는 추가 요금제를 참조 하는 구독도 삭제 될 때까지 상태로 유지 됩니다. 

- <!--2760466 – IS  ASDK --> 이 버전을 실행 하는 새 Azure Stack 환경에 설치한 경우이 경고는 나타냅니다 *활성화 필요* 표시 되지 않을 수 있습니다. [활성화](azure-stack-registration.md) 마켓플레이스 배포를 사용 하려면 먼저 필요 합니다.  

- <!-- TBD - IS ASDK --> 관리 구독 유형에 [1804 버전을 사용 하 여 도입 된](azure-stack-update-1804.md#new-features) 쓰일 수 없습니다. 구독 유형은 **구독을 계량**, 및 **소비 구독**합니다. 이러한 구독 유형에 1804 버전부터 새 Azure Stack 환경에서 표시 되지만 아직 사용할 준비가 완료 됩니다. 계속 사용 해야 합니다 **기본 공급자** 구독 유형입니다.

- <!-- 2403291 - IS ASDK --> 관리자 및 사용자 포털의 아래쪽에 가로 스크롤 막대를 사용 하 여가 없을 수 있습니다. 가로 스크롤 막대에 액세스할 수 없는 경우 블레이드의 이름을 선택 하 여 포털에서 이전 블레이드로 이동 하려면 이동 경로 상단에 있는 이동 경로 탐색 목록에서 확인 하려는 사용 하 여 포털의 왼쪽입니다.

  ![이동 경로 탐색](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> 관리자 포털에서 계산 또는 저장소 리소스를 보려면 하지 못할 수도 있습니다. 이 문제의 원인은 잘못 보고 성공으로 업데이트 하면 업데이트를 설치 하는 동안 오류입니다. 이 문제가 발생 하는 경우 Microsoft 고객 지원 서비스에 문의 하십시오.

- <!-- TBD - IS --> 포털에서 빈 대시보드를 볼 수 있습니다. 대시보드를 복구 하려면 포털의 오른쪽 위 모서리에서 기어 아이콘을 선택 하 고 선택한 **기본 설정 복원**합니다.

- <!-- TBD - IS ASDK --> 분리 된 리소스에서 사용자 구독 결과 삭제합니다. 대 안으로 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

- <!-- TBD - IS ASDK --> Azure Stack 포털을 사용 하 여 구독에 사용 권한을 확인할 수 없습니다. 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.


### <a name="health-and-monitoring"></a>상태 및 모니터링
- <!-- 1264761 - IS ASDK -->  에 대 한 경고를 표시 될 수 있습니다 합니다 **상태 컨트롤러** 다음 세부 정보는 구성 요소:  

   경고 # 1:
   - 비정상 인프라 역할 이름:
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 하트 비트 검색 프로그램 사용할 수 없는 경우 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.  

  # 2를 경고 합니다.
   - 비정상 인프라 역할 이름:
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 오류 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.

  모두 경고를 안전 하 게 무시할 수 있습니다 하 고 시간이 지남에 따라 자동으로 닫을 수 있습니다.  


- <!-- 2812138 | IS --> 에 대 한 경고가 표시 될 수 있습니다 **저장소** 다음 세부 정보는 구성 요소:

   - 이름: 저장소 서비스의 내부 통신 오류  
   - 심각도: 위험  
   - 구성 요소: 저장소  
   - 설명: 다음 노드에 요청을 보낼 때 저장소 서비스의 내부 통신 오류가 발생 했습니다.  

    경고를 안전 하 게 무시할 수 있지만 경고를 수동으로 종결 해야 합니다.

- <!-- 2368581 - IS. ASDK --> 메모리 부족 경고를 받게 테 넌 트 가상 머신을 사용 하 여 배포 하는 데 실패 한 경우 Azure Stack 운영자를 **패브릭 VM 만들기 오류**, 사용 가능한 메모리가 부족 합니다. Azure Stack 스탬프는 불가능 합니다. 사용 된 [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) 워크 로드에 대 한 사용 가능한 용량을 가장 잘 알아야 합니다.


### <a name="compute"></a>컴퓨팅

- <!-- 2724873 - IS --> PowerShell cmdlet을 사용 하는 경우 **시작 AzsScaleUnitNode** 하거나 **중지 AzsScaleunitNode** 배율 단위를 관리 하려면 시작 또는 중지 확장 단위를 첫 번째 시도가 실패할 수 있습니다. 첫 번째 실행에 실패 하면 cmdlet은 cmdlet을 두 번 실행 합니다. 작업을 완료 하 고 두 번째 실행 성공 해야 합니다. 

- <!-- 2494144 - IS, ASDK --> 일부 F 시리즈 VM 크기는 가상 머신 배포에 대 한 가상 머신 크기를 선택할 때 표시 되지 일부로 VM을 만들 때 크기 선택기입니다. 다음 VM 크기 선택기에 표시 되지 않습니다: *F8s_v2*, *F16s_v2*, *F32s_v2*, 및 *F64s_v2*합니다.  
  해결 방법으로 VM을 배포 하려면 다음 방법 중 하나를 사용 합니다. 각 메서드를 사용 하려면 VM 크기를 지정 해야 합니다.

  - **Azure Resource Manager 템플릿:** 템플릿을 사용 하는 경우 설정 된 *vmSize* 사용 하려는 VM 크기와 같아야 하는 템플릿에 합니다. 다음 항목을 사용 하는 VM을 배포 하는 예를 들어 합니다 *F32s_v2* 크기:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** 사용할 수는 [az vm 만들기](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 명령 및 유사한 매개 변수로 VM 크기를 지정할 `--size "Standard_F32s_v2"`합니다.

  - **PowerShell:** Powershell을 사용할 수 있습니다 [New-azurermvmconfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) 비슷합니다 VM 크기를 지정 하는 매개 변수를 사용 하 여 `-VMSize "Standard_F32s_v2"`입니다.


- <!-- TBD - IS ASDK --> 가상 머신 확장 집합에 대 한 크기 조정 설정을 포털에서 사용할 수 없는 경우 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이로 인해 사용 해야 합니다 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

- <!-- TBD - IS --> 가용성으로 이동 하 여 포털에서 집합을 만들면 **새로 만들기** > **계산** > **가용성 집합**를 만들 수 있습니다는 장애 도메인 및 업데이트 도메인 1을 사용 하 여 가용성 집합. 해결 방법으로, 새 가상 머신을 만들 때, 가용성 집합 내에서 또는 PowerShell, CLI를 사용 하 여 만들기 포털입니다.

- <!-- TBD - IS ASDK --> Azure Stack 사용자 포털에서 가상 컴퓨터를 만들 때 포털 잘못 된 DS 시리즈 VM에 연결할 수 있는 데이터 디스크 수를 표시 합니다. DS 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- <!-- TBD - IS ASDK --> VM 배포에 확장을 프로 비전 너무 오래 걸리는 경우 사용자는 할당 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 대신 프로 비전 제한을 하도록 해야 합니다.  

- <!-- 1662991 IS ASDK --> Azure Stack의 Linux VM 진단을 지원 되지 않습니다. 를 사용 하도록 설정 하는 VM 진단을 사용 하 여 Linux VM을 배포 하는 경우 배포가 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정한 경우에 배포가 실패 합니다.  

- <!-- 2724961- IS ASDK --> 등록할 때를 **Microsoft.Insight** 리소스 공급자 구독 설정에서 Windows VM 만들기 및 VM 개요 페이지 게스트 OS 진단 활성화를 사용 하 여 메트릭 데이터를 표시 하지 않습니다. 

   VM에 대 한 CPU 백분율 차트와 같은 메트릭 데이터를 찾으려면에서로 이동 합니다 **메트릭을** 블레이드에서 살펴보고, 지원 되는 모든 Windows VM 게스트 메트릭을 합니다.

### <a name="networking"></a>네트워킹  

- <!-- 1766332 - IS ASDK --> 아래 **네트워킹**를 클릭 하면 **VPN 게이트웨이 만들기** VPN 연결을 설정 하려면 **정책 기반** VPN 형식으로 나열 됩니다. 이 옵션을 선택 하지 마십시오. 만 **경로 기반** 옵션은 Azure Stack에서 지원 됩니다.

- <!-- 1902460 - IS ASDK --> Azure Stack 지원 단일 *로컬 네트워크 게이트웨이* IP 주소당 합니다. 모든 테 넌 트 구독에서 그렇습니다. 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만드는 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려는 시도가 차단 됩니다.

- <!-- 16309153 - IS ASDK --> DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*을 사용자 지정 DNS 서버 실패를 변경 합니다. 업데이트 된 설정은 해당 Vnet의 Vm에 푸시되 지 않습니다.

- <!-- 2702741 -  IS ASDK --> 동적 할당 메서드를 사용 하 여 배포 되는 공용 Ip 실행 중지-할당 취소 후에 유지 되도록 보장 되지 않습니다.

- <!-- 2529607 - IS ASDK --> Azure Stack 하는 동안 *암호 회전*에는 공용 IP 주소에 연결할 수 없는 2 ~ 5 분에 대 한 간격이 있습니다.

-   <!-- 2664148 - IS ASDK --> 여기서 테 넌 트에 액세스 하는 가상 컴퓨터는 S2S VPN 터널을 사용 하 여 시나리오에서는 온-프레미스 서브넷이 게이트웨이 이미 만든 후 로컬 네트워크 게이트웨이를 추가한 경우 연결 시도가 실패 하는 있는 경우를 발생할 수 있습니다. 


### <a name="sql-and-mysql"></a>SQL 및 MySQL



- <!-- No Number - IS, ASDK -->  공간 및 마침표를 포함 하 여 특수 문자를 사용할 수 없습니다는 **제품군** SQL 및 MySQL 리소스 공급자에 대 한 SKU를 만들 때 이름입니다. 

- <!-- TBD - IS --> 항목을 만드는 서버에서 해당 호스트 SQL 또는 MySQL 리소스 공급자에만 사용할 수 있습니다. 리소스 공급자에 의해 생성 되지 않은 호스트 서버에서 생성 하는 항목 일치 하지 않는 상태가 될 수 있습니다.  

> [!NOTE]  
> <!-- TBD - IS --> Azure Stack의이 버전으로 업데이트 한 후에 이전에 배포한 SQL 및 MySQL 리소스 공급자를 사용 하도록 계속 수 있습니다.  새 릴리스를 사용할 수 있을 때 SQL 및 MySQL를 업데이트 하는 것이 좋습니다. 예: Azure Stack에 SQL 및 MySQL 리소스 공급자 업데이트를 순차적으로 적용 됩니다. 예를 들어 1804 버전을 사용 하는 경우 먼저 1805, 버전을 적용 하 고 1807으로 업데이트 합니다.  
>  
> 이 업데이트의 설치를 현재 사용자가 SQL 또는 MySQL 리소스 공급자 사용에 영향을 주지 않습니다. 
> 사용할 리소스 공급자의 버전에 관계 없이 해당 데이터베이스의 사용자 데이터는 그대로 유지, 및 계속 액세스할 수 있습니다.  

### <a name="app-service"></a>App Service

- <!-- 2352906 - IS ASDK --> 사용자는 구독에 해당 첫 번째 Azure Function 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

- <!-- 2489178 - IS ASDK --> (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.

- <!-- TBD - IS ASDK --> App Service에 배포할 수 있습니다 합니다 *기본 공급자 구독* 지금은 합니다. 향후 업데이트를 새 App Service 배포 *구독을 계량* 는 Azure Stack 1804에서 도입 되었습니다. 계량 사용에 대 한 지원 되는 경우이 새 구독 형식으로 기존의 모든 배포를 마이그레이션합니다.


### <a name="usage"></a>사용 현황  
- <!-- TBD - IS ASDK --> 사용량 공용 IP 주소 사용 측정기 데이터를 보여 줍니다 동일 *EventDateTime* 대신 각 레코드에 대 한 값을 *TimeDate* 레코드를 만든 경우를 보여 주는 스탬프. 현재 공용 IP 주소 사용을 정확 하 게 설명 하는 데이 데이터를 사용할 수 없습니다.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>업데이트 다운로드
Azure Stack 1807 업데이트 패키지를 다운로드할 수 있습니다 [여기](https://aka.ms/azurestackupdatedownload)합니다.


## <a name="next-steps"></a>다음 단계
- Azure Stack 통합 시스템 및 지원 되는 상태 시스템을 유지 하기 위해 해야 할 새로운 서비스의 정책을 참조 하세요 [Azure Stack 서비스 정책](azure-stack-servicing-policy.md)합니다.  
- 권한 있는 끝점 (PEP)를 사용 하 여 모니터링 하 고 업데이트를 다시 시작, 참조 [권한 있는 끝점을 사용 하 여 Azure Stack의 업데이트 모니터링](azure-stack-monitor-update.md)합니다.  
- Azure Stack의 업데이트 관리 개요를 참조 하세요 [Azure Stack 개요에 대 한 업데이트 관리](azure-stack-updates.md)합니다.  
- Azure Stack을 사용 하 여 업데이트를 적용 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Stack의 업데이트 적용](azure-stack-apply-updates.md)합니다.  
