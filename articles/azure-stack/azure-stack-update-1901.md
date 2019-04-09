---
title: Azure Stack 1901 업데이트 | Microsoft Docs
description: Azure Stack 통합 시스템의 경우 새로운 기능을 포함 하 여 1901 업데이트에 대 한 알려진된 문제 및 업데이트를 다운로드 하는 위치를 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 03/27/2019
ms.openlocfilehash: bf355aec2a76a42986320a74447860a8baa968ef
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257403"
---
# <a name="azure-stack-1901-update"></a>Azure Stack 1901 업데이트

*적용 대상 Azure Stack 통합 시스템*

이 문서에서는 1901 업데이트 패키지의 내용을 설명 합니다. 업데이트는 향상 된 기능, 수정 및 Azure Stack의이 버전에 대 한 새로운 기능을 포함합니다. 또한이 문서는이 릴리스의 알려진된 문제를 설명 하 고 업데이트를 다운로드 하려면 링크를 포함 합니다. 알려진된 문제는 업데이트 프로세스를 직접 관련 된 문제 및 문제 (설치 후) 빌드를 사용 하 여으로 구분 됩니다.

> [!IMPORTANT]  
> Azure Stack 통합 시스템에만이 업데이트 패키지가 됩니다. Azure Stack 개발 키트에는이 업데이트 패키지를 적용 되지 않습니다.

## <a name="build-reference"></a>빌드 참조

Azure Stack 1901 업데이트 빌드 번호는 **1.1901.0.95** 하거나 **1.1901.0.99** 2019 년 2 월 26 일 후입니다. 다음 참고를 참조 하세요.

> [!IMPORTANT]  
> Microsoft에서 1901 년에 1811 (1.1811.0.101)에서 업데이트 하는 고객은 영향을 줄 수 있는 문제를 검색 및 문제를 해결 하는 업데이트 된 1901 패키지를 출시 했습니다: 1.1901.0.99, 1.1901.0.95에서 업데이트를 작성 합니다. 1.1901.0.95를 이미 업데이트는 고객에 게는 추가 작업을 수행할 필요가 없습니다.
>
> 1811에 있는 연결 된 고객 관리자 포털에서 사용할 수 있는 새 1901 (1.1901.0.99) 패키지를 자동으로 표시 됩니다 및 준비 하는 경우 설치 해야 합니다. 연결이 끊긴된 고객을 다운로드 하 고 동일한 프로세스를 사용 하 여 새 1901 패키지를 가져올 수 [여기에 설명 된](azure-stack-apply-updates.md)합니다.
>
> 다음 전체 또는 핫픽스 패키지를 설치 하는 경우 버전 1901 중 하나를 사용 하 여 고객에 게는 영향이 없습니다.

## <a name="hotfixes"></a>핫픽스

Azure Stack에는 핫픽스를 정기적으로 해제합니다. 설치 해야 합니다 [최신 Azure Stack 핫픽스](#azure-stack-hotfixes) 1901로 Azure Stack을 업데이트 하기 전에 1811에 대 한 합니다.

Azure Stack 핫픽스 Azure Stack 통합 시스템에 적용할 수만 있습니다. ASDK에서 핫픽스를 설치 하지 마십시오.

> [!TIP]  
> 다음을 구독할 *RSS* 또는 *Atom* Azure Stack 핫픽스를 피드 합니다.
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack 핫픽스

이미 1901 및 모든 핫픽스를 설치 하지 않은 아직 할 수 있습니다 [1902를 직접 설치](azure-stack-update-1902.md), 첫 번째 하지 않고 1901 핫픽스를 설치 합니다.

- **1809**: [4481548 – Azure Stack 1.1809.12.114 핫픽스 (kb)](https://support.microsoft.com/help/4481548/)
- **1811**: 현재 핫픽스 사용 가능 합니다.
- **1901**: [4495662 – Azure Stack 1.1901.3.105 핫픽스 (kb)](https://support.microsoft.com/help/4495662)

## <a name="prerequisites"></a>필수 조건

> [!IMPORTANT]
> 설치 합니다 [최신 Azure Stack 핫픽스](#azure-stack-hotfixes) 1901를 업데이트 하기 전에 1811 (있는 경우)에 대 한 합니다. 1901를 이미 있는 모든 핫픽스를 아직 설치 하지 않은 경우 먼저 1901 핫픽스를 설치 하지 않고 직접 1902를 설치할 수 있습니다.

- 이 업데이트의 설치를 시작 하기 전에 실행할 [테스트 AzureStack](azure-stack-diagnostic-test.md) 에 Azure Stack의 상태를 확인 하 고 발견 된 작동 문제를 해결 하려면 다음 매개 변수를 사용 하 여 모든 경고 및 오류를 포함 합니다. 또한 활성 경고를 검토 하 고 작업을 필요로 하는 해결:

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- Azure Stack에서 System Center Operations Manager (SCOM)을 관리 되는 경우에 1901 적용 하기 전에 1.0.3.11 버전으로 Microsoft Azure Stack 용 관리 팩을 업데이트 해야 합니다.

## <a name="new-features"></a>새로운 기능

이 업데이트는 다음과 같은 새로운 기능 및 Azure Stack에 대 한 향상 된 기능을 포함합니다.

- Azure Stack 사용의 관리 되는 이미지 앞으로 Vm 디스크만 만들 수 있습니다 관리 되는 일반화 된 VM (둘 다 비관리 및 관리)에 관리 되는 이미지를 만들 수 있습니다. 자세한 내용은 [Azure Stack의 Managed Disks](user/azure-stack-managed-disk-considerations.md#managed-images)합니다.

- **AzureRm 2.4.0**
   * **AzureRm.Profile**  
         버그 수정- `Import-AzureRmContext` 저장된 된 토큰을 올바르게 역직렬화 합니다.  
   * **AzureRm.Resources**  
         버그 수정- `Get-AzureRmResource` 쿼리 사례 리소스 종류별로 소문자를 구분 합니다.  
   * **Azure.Storage**  
         AzureRm의 롤업 모듈 이제 이미 게시 된 버전 4.5.0 지원 합니다 **api 버전 2017-07-29**합니다.  
   * **AzureRm.Storage**  
         AzureRm의 롤업 모듈 이제 지원 5.0.4 이미 게시 된 버전을 포함 합니다 **api 버전 2017-10-01**합니다.  
   * **AzureRm.Compute**  
         추가 간단한 매개 변수 집합에 `New-AzureRmVM` 하 고 `New-AzureRmVmss`, `-Image` 매개 변수 지정 사용자 이미지를 지원 합니다.  
   * **AzureRm.Insights**  
         AzureRm의 롤업 모듈 이제 이미 게시 된 버전이 5.1.5 지원 합니다 **api-version 2018-01-01** 메트릭을 메트릭 정의 리소스 종류에 대 한 합니다.

- **AzureStack 1.7.1** 이 주요 변경 내용 릴리스 합니다. 호환성이 손상되는 변경에 대한 자세한 내용은 https://aka.ms/azspshmigration171를 참조하세요.
   * **Azs.Backup.Admin 모듈**  
         호환성이 손상되는 변경 내용: Backup이 인증서 기반 암호화 모드로 변경됩니다. 대칭 키에 대한 지원은 사용되지 않습니다.  
   * **Azs.Fabric.Admin 모듈**  
         `Get-AzsInfrastructureVolume` 사용 되지 않습니다. 새 cmdlet을 사용 하 여 `Get-AzsVolume`입니다.  
         `Get-AzsStorageSystem` 사용 되지 않습니다.  새 cmdlet을 사용 하 여 `Get-AzsStorageSubSystem`입니다.  
         `Get-AzsStoragePool` 사용 되지 않습니다. `StorageSubSystem` 용량 속성을 포함 하는 개체입니다.  
   * **Azs.Compute.Admin 모듈**  
         버그 수정- `Add-AzsPlatformImage`, `Get-AzsPlatformImage`: 호출 `ConvertTo-PlatformImageObject` 성공 경로에 합니다.  
         BugFix - `Add-AzsVmExtension`, `Get-AzsVmExtension`: 성공 경로 에서만 ConvertTo VmExtensionObject를 호출 합니다.  
   * **Azs.Storage.Admin 모듈**  
         버그 수정-새 저장소 할당량 제공 없는 경우 기본값을 사용 합니다.

업데이트 된 모듈에 대 한 참조를 참조 하세요 [Azure Stack 모듈 참조](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0&viewFallbackFrom=azurestackps-1.7.0)합니다.

## <a name="fixed-issues"></a>해결된 문제

- 포털에서는 Azure Stack에서 지원 되지 않는 정책 기반 VPN gateway를 만드는 옵션을 보여 주었습니다 문제를 해결 했습니다. 이 옵션이 포털에서 제거 되었습니다.

<!-- 16523695 – IS, ASDK -->
- 가상 네트워크에 대 한 DNS 설정을 업데이트 한 후에 문제를 해결 했습니다 **사용 하 여 Azure Stack DNS** 하 **사용자 지정 DNS**, 새 설정을 사용 하 여 인스턴스를 업데이트 하지 못했습니다.

- <!-- 3235634 – IS, ASDK -->
  포함 된 크기를 사용 하 여 Vm이 배포의 문제를 해결를 **v2** 접미사; 예를 들어 **Standard_A2_v2**접미사를 지정 하는 필수 **Standard_A2_v2** ( 소문자 v)입니다. 전역 Azure를 사용 하면 이제 사용할 수 있습니다 **Standard_A2_V2** (V 대문자).

<!--  2795678 – IS, ASDK --> 
- 포털 (DS, Ds_v2, FS, FSv2) 프리미엄 VM 크기의 가상 머신 (Vm)를 만드는 데 때 경고를 생성 하는 문제가 수정 되었습니다. 표준 저장소 계정에 VM은 만들었습니다. 이 영향을 미치지 않습니다 기능적으로 IOPs 또는 대금 청구, 있지만 경고가 수정 되었습니다.

<!-- 1264761 - IS ASDK -->  
- 사용 하 여 문제를 해결 합니다 **상태 컨트롤러** 다음 경고가 발생 하는 구성 요소입니다. 경고는 안전 하 게 무시할 수 있습니다.

    - 경고 # 1:
       - 이름:  인프라 역할 비정상
       - 심각도: Warning
       - 구성 요소입니다. 상태 컨트롤러
       - 설명: 상태 컨트롤러 하트 비트 검색 프로그램을 사용할 수 없는 경우 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.  

    - # 2를 경고 합니다.
       - 이름:  인프라 역할 비정상
       - 심각도: Warning
       - 구성 요소입니다. 상태 컨트롤러
       - 설명: 상태 컨트롤러 오류 스캐너 제공 되지 않습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.


<!-- 3507629 - IS, ASDK --> 
- Managed Disks 할당량 값을 설정할 때 문제를 해결 했습니다 [계산 할당량 형식](azure-stack-quota-types.md#compute-quota-types) 0으로 2048 GiB의 기본 값에 해당 됩니다. 할당량 값 0에 이제 다음이 적용 됩니다.

<!-- 2724873 - IS --> 
- PowerShell cmdlet을 사용 하 여 문제를 해결 **시작 AzsScaleUnitNode** 또는 **중지 AzsScaleUnitNode** 시작 또는 중지 확장 단위를 첫 번째 시도가 실패할 배율 단위를 관리할 수 있습니다.

<!-- 2724961- IS ASDK --> 
- 등록 하는 문제를 해결 합니다 **Microsoft.Insight** 구독 설정에서 리소스 공급자 및 게스트 OS 진단 활성화를 사용 하 여 Windows VM을 만든 하지만 VM 개요 페이지에서 CPU 백분율 차트는 표시 되지 않았습니다 메트릭 데이터입니다. 데이터를 잘못 표시 됩니다.

- 실행에서 문제를 해결 합니다 **Get AzureStackLog** 실행 하면 cmdlet이 실패 했습니다 **테스트 AzureStack** 같은 권한 있는 끝점 (PEP) 세션에서. 이제 실행 하는 동일한 PEP 세션을 사용할 수 있습니다 **테스트 AzureStack**합니다.

<!-- bug 3615401, IS -->
- Scheduler 서비스 들어가게 자동 백업 문제 해결된 상태를 예기치 않게 비활성화 합니다. 

<!--2850083, IS ASDK -->
- 제거 된 **게이트웨이 재설정** 단추는 단추를 클릭 한 경우 오류가 발생 했습니다는 Azure Stack 포털에서 합니다. Azure Stack에 전용된 VM 인스턴스가 아니라 다중 테 넌 트 게이트웨이 VPN Gateway를 각 테 넌 트에 대 한 혼동을 방지 하려면 제거 된 Azure Stack에 없는 기능을 제공 하는이 단추입니다. 

<!-- 3209594, IS ASDK -->
- 제거 된 **효과적인 보안 규칙** 에서 링크는 **네트워킹 속성** 블레이드에서이 기능으로 Azure Stack에서 지원 되지 않습니다. 링크가 있는 것 처럼이이 기능을 지원 하지만 작동 하지 않는 수 있었습니다. 혼동을 완화 하기 위해 링크를 제거 했습니다.

<!-- 3139614 | IS -->
- 업데이트는 OEM에서 Azure Stack에 적용 된 후 나타나는 문제를 해결 합니다 **업데이트 사용 가능** 알림 Azure Stack 관리자 포털에 표시 되지 않았습니다.

## <a name="changes"></a>변경 내용

<!-- 3083238 IS -->
- 이 업데이트에 향상 된 보안 기능 디렉터리 서비스 역할의 백업 크기가 증가 하는 발생합니다. 외부 저장소 위치에 대 한 지침을 크기 조정 업데이트에 대 한 참조를 [인프라 backup 설명서](azure-stack-backup-reference.md#storage-location-sizing)합니다. 이 변경으로 인해 더 큰 크기 데이터 전송으로 인해 백업을 완료 하려면 시간이 더 길어집니다. 이 변경에 통합된 시스템에 영향을 줍니다. 

- 2019 년 1 월부터, Kubernetes 클러스터에서 Active Directory Federated Services (AD FS)에 등록 (인터넷 액세스 필요) 하는 연결 된 Azure Stack 스탬프를 배포할 수 있습니다. 지침을 따릅니다 [여기](azure-stack-solution-template-kubernetes-cluster-add.md) 새 Kubernetes 마켓플레이스 항목을 다운로드 합니다. 지침을 따릅니다 [여기](user/azure-stack-solution-template-kubernetes-adfs.md) Kubernetes 클러스터를 배포 합니다. 참고 추가 또는 AD FS 대상 시스템 인지 여부를 나타내는 새 매개 변수를 등록 합니다. AD FS 인 경우 새 필드는 배포 인증서 저장 되는 Key Vault 매개 변수를 입력 됩니다.

   AD FS를 사용 하 여도 지 원하는 참고, Kubernetes 클러스터 배포는 인터넷 액세스가 필요 합니다.

- Azure Stack을 업데이트 또는 핫픽스를 설치한 후 새 기능 추가 될 수 있습니다 하나 이상의 id 응용 프로그램에 부여 될 새 권한이 필요 합니다. 이러한 권한 부여 관리 홈 디렉터리에 액세스할 수 있어야 하며 따라서이 수행할 수 없는 자동으로 합니다. 예를 들면 다음과 같습니다.

   ```powershell
   $adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
   $homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com" # This is the primary tenant Azure Stack is registered to

   Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
     -DirectoryTenantName $homeDirectoryTenantName -Verbose
   ```

- 정확 하 게 Azure Stack 용량을 계획에 새 고려해 야가 있습니다. 1901 업데이트를 사용 하 여 만들 수 있는 가상 머신의 총 수에 제한이 이제 됩니다.  이 제한은 솔루션 안정성 문제를 방지 하려면 일시적인 것입니다. 소스 Vm 수를 높게에서 안정성 문제를 해결 하는 하지만 수정에 대 한 일정 아직 확인 되지 않았습니다. 1901 업데이트에서는 이제 됩니다는 서버당 60 Vm의 총 솔루션 최대 700입니다.  예를 들어, 8 서버 Azure Stack VM 한도 480 (8 * 60) 것입니다.  12-16 서버의 Azure Stack 솔루션도 700 것입니다. 이 제한은 염두, 모든 계산 용량 고려 사항 복원 력 예약 및 CPU와 같은 가상 스탬프를 유지 하려는 운영자는 실제 비율을 만들었습니다. 자세한 내용은 capacity planner의 새 릴리스를 참조 하세요.  
VM 규모 한도 도달 하는 경우에 다음 오류 코드 결과적으로 반환 됩니다. VMsPerScaleUnitLimitExceeded, VMsPerScaleUnitNodeLimitExceeded. 
 

- 계산 API 버전 2017-12-01로 증가 했습니다.

- 인프라 백업 공개 키만을 사용 하 여 이제 인증서를 요구 (합니다. CER) 백업 데이터의 암호화에 대 한 합니다. 대칭 암호화 키 지원 1901부터 사용 되지 않습니다. 1901를 업데이트 하기 전에 인프라 백업이 구성 된 경우 암호화 키 위치에 유지 됩니다. 최소한 2를 사용 하 여 이전 버전과 호환성 지원을 백업 설정을 업데이트 하려면 업데이트 자세히 해야 합니다. 자세한 내용은 [Azure Stack 인프라 백업 모범 사례](azure-stack-backup-best-practices.md)합니다.

## <a name="common-vulnerabilities-and-exposures"></a>일반적인 취약점 및 exposures

이 업데이트에는 다음 보안 업데이트를 설치합니다.  

- [CVE-2018-8477](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8477)
- [CVE-2018-8514](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8514)
- [CVE-2018-8580](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8580)
- [CVE-2018-8595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8595)
- [CVE-2018-8596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8596)
- [CVE-2018-8598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8598)
- [CVE-2018-8621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8621)
- [CVE-2018-8622](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8622)
- [CVE-2018-8627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8627)
- [CVE-2018-8637](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8637)
- [CVE-2018-8638](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8638)
- [ADV190001](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190001)
- [CVE-2019-0536](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0536)
- [CVE-2019-0537](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0537)
- [CVE-2019-0545](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0545)
- [CVE-2019-0549](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0549)
- [CVE-2019-0553](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0553)
- [CVE-2019-0554](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0554)
- [CVE-2019-0559](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0559)
- [CVE-2019-0560](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0560)
- [CVE-2019-0561](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0561)
- [CVE-2019-0569](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0569)
- [CVE-2019-0585](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0585)
- [CVE-2019-0588](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0588)


이러한 취약점에 대 한 자세한 내용은 위의 링크를 클릭 하거나 Microsoft 기술 자료 문서를 참조 하세요 [4480977](https://support.microsoft.com/en-us/help/4480977)합니다.

## <a name="known-issues-with-the-update-process"></a>업데이트 프로세스를 사용 하 여 알려진된 문제

- 실행 하는 경우 [테스트 AzureStack](azure-stack-diagnostic-test.md)경우는 **AzsInfraRoleSummary** 또는 **AzsPortalApiSummary** 테스트가 실패, 실행 하는 메시지가  **테스트 AzureStack** 사용 하 여는 `-Repair` 플래그입니다.  이 명령을 실행 하는 경우 다음 오류 메시지와 함께 실패 합니다.  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`

- 실행할 때 [테스트 AzureStack](azure-stack-diagnostic-test.md), 베이스 보드 관리 컨트롤러 (BMC)에서 경고 메시지가 표시 됩니다. 이 경고를 안전 하 게 무시할 수 있습니다.

- <!-- 2468613 - IS --> 이 업데이트를 설치 하는 동안 경고 제목으로 표시 될 수 있습니다 `Error – Template for FaultType UserAccounts.New is missing.` 이러한 경고를 안전 하 게 무시할 수 있습니다. 이 업데이트의 설치가 완료 된 후 경고를 자동으로 닫습니다.

## <a name="post-update-steps"></a>업데이트 후 단계

- 이 업데이트의 설치가 끝나면 모든 적용 가능한 핫픽스를 설치 합니다. 자세한 내용은 [핫픽스](#hotfixes), 뿐만 아니라 우리 [서비스 정책](azure-stack-servicing-policy.md)합니다.  

- 암호화 미사용 데이터를 검색 하 고 Azure Stack 배포 외부에서 안전 하 게 저장 합니다. 수행 합니다 [키를 검색 하는 방법에 대 한 지침](azure-stack-security-bitlocker.md)합니다.

## <a name="known-issues-post-installation"></a>알려진된 문제 (사후 설치)

이 빌드 버전에 대 한 설치 후 알려진된 문제는 다음과 같습니다.

### <a name="portal"></a>포털

<!-- 2930820 - IS ASDK --> 
- 관리자와 사용자 포털에서 "Docker"를 검색 하는 경우 항목을 올바르게 반환 됩니다. Azure Stack에서 사용할 수 없는 합니다. 만들 하려고 하면 오류가 표시 된 블레이드가 표시 됩니다. 

<!-- 2931230 – IS  ASDK --> 
- 사용자 구독에서 해당 계획을 제거 하는 경우에는 추가 요금제는 사용자 구독에 추가 되는 계획을 삭제할 수 없습니다. 계획에는 추가 요금제를 참조 하는 구독도 삭제 될 때까지 상태로 유지 됩니다. 

<!-- TBD - IS ASDK --> 
- 1804 버전을 사용 하 여 도입 된 관리 구독 유형에 사용할 수 없습니다. 구독 유형은 **구독을 계량**, 및 **소비 구독**합니다. 이러한 구독 유형에 1804 버전부터 새 Azure Stack 환경에서 표시 되지만 아직 사용할 준비가 완료 됩니다. 계속 사용 해야 합니다 **기본 공급자** 구독 유형입니다.

<!-- 3557860 - IS ASDK --> 
- 분리 된 리소스에서 사용자 구독 결과 삭제합니다. 대 안으로 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

<!-- 1663805 - IS ASDK --> 
- Azure Stack 포털을 사용 하 여 구독에 사용 권한을 확인할 수 없습니다. 대 안으로 사용 하 여 [사용 권한을 확인 하려면 PowerShell](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan)합니다.

<!-- ### Health and monitoring -->

### <a name="compute"></a>컴퓨팅

- 새 Windows 가상 머신 (VM)를 만들 때 다음 오류가 표시 될 수 있습니다.

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   오류는 VM에서 부팅 진단을 사용 하도록 설정 해도 부트 진단 저장소 계정을 삭제 하는 경우에 발생 합니다. 이 문제를 해결 하려면 이전에 사용한 동일한 이름 사용 하 여 저장소 계정을 다시 만듭니다.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- 가상 머신 확장 집합 (VMSS) 생성 환경에 대 한 배포 옵션으로 7.2 CentOS 기반을 제공합니다. 모두 배포에 대 한 다른 운영 체제를 선택 하거나 다운로드 하 여 marketplace에서 배포 하기 전에 다른 CentOS 이미지를 지정 하는 Azure Resource Manager 템플릿을 사용 하 여 해당 이미지를 Azure Stack에서 사용할 수 없는 때문에 연산자입니다.  

<!-- TBD - IS ASDK --> 
- 업데이트 된 1901를 적용 한 후, Managed Disks를 사용 하 여 Vm을 배포 하는 경우 다음과 같은 문제가 발생할 수 있습니다.

   - Managed Disks를 사용 하 여 VM 배포 1808 업데이트 하기 전에 구독을 만든 경우 내부 오류 메시지와 함께 실패할 수 있습니다. 오류를 해결 하려면 각 구독에 대해 다음이 단계를 수행 합니다.
      1. 테 넌 트 포털에서로 이동 **구독** 구독을 찾습니다. 선택 **리소스 공급자**을 선택한 후 **Microsoft.Compute**를 클릭 하 고 **re-register**합니다.
      2. 로 동일한 구독에 따라 **액세스 제어 (IAM)**, 되어 있는지 확인 하 고 **AzureStack DiskRP 클라이언트** 나열 됩니다.
   - 다중 테 넌 트 환경에 구성한 게스트 디렉터리와 연결 된 구독에서 Vm을 배포할 내부 오류 메시지와 함께 실패할 수 있습니다. 오류를 해결 하려면에서 다음이 단계를 수행 [이 문서에서는](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) 각 게스트 디렉터리를 다시 구성 합니다.

- 사용 하도록 설정 하는 SSH 인증을 사용 하 여 만든 Ubuntu 18.04 VM은 SSH 키를 사용 하 여 로그인 할 수 없습니다. 이 문제를 해결 하려면 Linux 확장에 대 한 VM 액세스 프로 비전 한 후 SSH 키를 구현 하는 데 또는 암호 기반 인증을 사용 합니다.

### <a name="networking"></a>네트워킹  

<!-- 3239127 - IS, ASDK -->
- Azure Stack 포털에 VM 인스턴스에 연결 된 네트워크 어댑터에 바인딩되어 있는 IP 구성에 대 한 고정 IP 주소를 변경 하면 나타납니다 없다는 경고 메시지 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    이 메시지를 안전 하 게 무시할 수 있습니다. IP 주소는 VM 인스턴스 다시 시작 하지 않습니다 하는 경우에 변경 됩니다.

<!-- 3632798 - IS, ASDK -->
- 인바운드 보안 규칙을 추가 하 고 선택 하는 경우 포털에서 **서비스 태그** 원본으로 여러 옵션에 표시 되는 **소스 태그가** Azure Stack에 대해 사용할 수 없는 목록입니다. Azure Stack에서 사용할 수 있는 유일한 옵션 아래와 같습니다.

  - **인터넷**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
    다른 옵션으로 Azure Stack에서 소스 태그는 지원 되지 않습니다. 마찬가지로, 아웃 바운드 보안 규칙을 추가 하 고 선택 하는 경우 **서비스 태그** 옵션에 대 한 동일한 목록 대상으로 **소스 태그가** 표시 됩니다. 유효한 옵션은 동일 **소스 태그가**이전 목록에 설명 된 대로 합니다.

- 네트워크 보안 그룹 (Nsg) 동일한 방식으로 전체 Azure에서 Azure Stack에서 작동 하지 않습니다. Azure에서 하나의 NSG 규칙에서 여러 포트를 설정할 수 있습니다 (포털, PowerShell을 사용 하 여 및 Resource Manager 템플릿). 그러나 Azure Stack에서 설정할 수 없습니다 여러 포트에서 포털을 통해 NSG 규칙을 하나입니다. 이 문제를 해결 하려면 이러한 추가 규칙을 설정 하는 Resource Manager 템플릿 또는 PowerShell을 사용 합니다.

<!-- 3203799 - IS, ASDK -->
- Azure Stack 인스턴스 크기에 관계 없이 현재 4 개 이상의 네트워크 인터페이스 (Nic) VM 인스턴스에 연결 하는 것을 지원 하지 않습니다.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- 구독에서 첫 번째 Azure Function을 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>syslog 
- Syslog 구성을 유지 되지 않습니다는 업데이트 주기를 통해 해당 구성과 전달 되는 것을 중지 하려면 syslog 메시지 손실 syslog 클라이언트에서 결과. 이 문제는 syslog 클라이언트 (1809)의 GA 이후 Azure Stack의 모든 버전에 적용 됩니다.
Azure Stack 업데이트를 적용 한 후 클라이언트를 syslog를 다시 구성 하려면이 문제를 해결 합니다.

## <a name="download-the-update"></a>업데이트 다운로드

Azure Stack 1901 업데이트 패키지를 다운로드할 수 있습니다 [여기](https://aka.ms/azurestackupdatedownload)합니다. 

연결 된 시나리오 에서만 Azure Stack 배포 주기적으로 보안된 끝점을 확인 하 고 자동으로 클라우드를 위한 사용 가능한 업데이트가 있는 경우을 알립니다. 자세한 내용은 [Azure Stack에 대 한 업데이트 관리](azure-stack-updates.md#using-the-update-tile-to-manage-updates)합니다.

## <a name="next-steps"></a>다음 단계

- Azure Stack의 업데이트 관리 개요를 참조 하세요 [Azure Stack 개요에 대 한 업데이트 관리](azure-stack-updates.md)합니다.  
- Azure Stack을 사용 하 여 업데이트를 적용 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Stack의 업데이트 적용](azure-stack-apply-updates.md)합니다.
- Azure Stack 통합 시스템 및 지원 되는 상태 시스템을 유지 하기 위해 해야 할 새로운 서비스의 정책을 참조 하세요 [Azure Stack 서비스 정책](azure-stack-servicing-policy.md)합니다.  
- 권한 있는 끝점 (PEP)를 사용 하 여 모니터링 하 고 업데이트를 다시 시작, 참조 [권한 있는 끝점을 사용 하 여 Azure Stack의 업데이트 모니터링](azure-stack-monitor-update.md)합니다.  
