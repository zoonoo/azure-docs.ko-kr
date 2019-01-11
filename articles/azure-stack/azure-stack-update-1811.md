---
title: Azure Stack 1811 업데이트 | Microsoft Docs
description: Azure Stack 통합 시스템의 경우 새로운 기능을 포함 하 여 1811 업데이트에 대 한 알려진된 문제 및 업데이트를 다운로드 하는 위치를 알아봅니다.
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
ms.date: 01/11/2019
ms.author: sethm
ms.reviewer: adepue
ms.openlocfilehash: f5826b2a6935bb448a7a3ef94d9a5f27f1ed9426
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214592"
---
# <a name="azure-stack-1811-update"></a>Azure Stack 1811 업데이트

*적용 대상: Azure Stack 통합 시스템*

이 문서에서는 1811 업데이트 패키지의 내용을 설명 합니다. 업데이트 패키지에는 향상 된 기능, 수정 및 Azure Stack의이 버전에 대 한 새 기능이 포함 됩니다. 이 문서는 또한이 릴리스의 알려진된 문제를 설명 하 고 업데이트를 다운로드할 수 있도록 링크가 포함 되어 있습니다. 알려진된 문제는 업데이트 프로세스를 직접 관련 된 문제 및 문제 (설치 후) 빌드를 사용 하 여으로 구분 됩니다.

> [!IMPORTANT]  
> Azure Stack 통합 시스템에만이 업데이트 패키지가 됩니다. Azure Stack 개발 키트에는이 업데이트 패키지를 적용 되지 않습니다.

## <a name="build-reference"></a>빌드 참조

Azure Stack 1811 업데이트 빌드 번호는 **1.1811.0.101**합니다.

## <a name="hotfixes"></a>핫픽스

Azure Stack에는 핫픽스를 정기적으로 해제합니다. 설치 해야 합니다 [최신 Azure Stack 핫픽스](#azure-stack-hotfixes) 1811로 Azure Stack을 업데이트 하기 전에 1809에 대 한 합니다.

> [!TIP]  
> 다음을 구독할 *RRS* 또는 *Atom* Azure Stack 핫픽스를 피드 합니다.
> - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
> - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...

### <a name="azure-stack-hotfixes"></a>Azure Stack 핫픽스

- **1809**: [4481548 – Azure Stack 1.1809.12.114 핫픽스 (kb)](https://support.microsoft.com/help/4481548/)
- **1811**: 현재 핫픽스 사용 가능 합니다.

## <a name="prerequisites"></a>필수 조건

> [!IMPORTANT]
> 1811 업데이트를 설치 하는 동안 관리자 포털의 모든 인스턴스를 닫았는지 확인 해야 합니다. 사용자 포털을 열어둘 수 있지만 관리 포털을 종료 해야 합니다.

- Azure Stack 확장 호스트에 대 한 준비 된 Azure Stack 배포를 가져옵니다. 다음 지침을 사용 하 여 시스템을 준비 합니다. [Azure Stack에 대 한 확장 호스트에 대 한 준비](azure-stack-extension-host-prepare.md)합니다. 
 
- 설치 합니다 [최신 Azure Stack 핫픽스](#azure-stack-hotfixes) 1811를 업데이트 하기 전에 1809에 대 한 합니다.

- 이 업데이트의 설치를 시작 하기 전에 실행할 [테스트 AzureStack](azure-stack-diagnostic-test.md) 에 Azure Stack의 상태를 확인 하 고 발견 된 작동 문제를 해결 하려면 다음 매개 변수를 사용 하 여 모든 경고 및 오류를 포함 합니다. 또한 활성 경고를 검토 하 고 작업을 필요로 하는 해결 합니다.  

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

    확장 호스트 요구 사항을 충족 하지 않은 경우는 `Test-AzureStack` 출력에는 다음과 같은 메시지가 표시 됩니다. 
  
    `To proceed with installation of the 1811 update, you will need to import 
    the SSL certificates required for Extension Host, which simplifies network 
    integration and increases the security posture of Azure Stack. Refer to this 
    link to prepare for Extension Host:
    https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare`

- Azure Stack 환경에 올바르게 필수 확장 호스트 인증서를 가져왔는지 해야 하는 Azure Stack 1811 업데이트입니다. 1811 업데이트의 설치를 계속 하려면 확장 호스트에 필요한 SSL 인증서를 가져와야 합니다. 참조 된 인증서를 가져오려면 [이 섹션에서는](azure-stack-extension-host-prepare.md#import-extension-host-certificates)합니다.

    모든 경고를 무시 하 고 1811 업데이트를 설치 하도록 선택할 경우 업데이트는 약 1 시간 동안 다음 메시지와 함께 실패 합니다.   
 
    `The required SSL certificates for the Extension Host have not been found.
    The Azure Stack update will halt. Refer to this link to prepare for 
    Extension Host: https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare,
    then resume the update.
    Exception: The Certificate path does not exist: [certificate path here]` 
 
    제대로 필수 확장 호스트 인증서를 가져온 후 관리자 포털에서 1811 업데이트를 다시 시작할 수 있습니다. Microsoft Azure Stack 운영자 업데이트 과정에서 유지 관리 기간을 예약 하려면 조언, 하는 동안 확장 호스트 인증서 누락으로 인 한 실패 영향을 주지 않아야 기존 워크 로드 또는 서비스입니다.  

    이 업데이트를 설치 하는 동안 Azure Stack 사용자 포털을 사용할 수 없는 경우 확장 호스트를 구성 하는 동안 확장 호스트의 구성에는 최대 5 시간까지 걸릴 수 있습니다. 이 시간 동안 업데이트 상태를 확인하거나, [Azure Stack 관리자 PowerShell 또는 권한 있는 엔드포인트](azure-stack-monitor-update.md)를 사용하여 실패한 업데이트 설치를 다시 시작할 수 있습니다.

## <a name="new-features"></a>새로운 기능

이 업데이트는 다음과 같은 새로운 기능 및 Azure Stack에 대 한 향상 된 기능을 포함합니다.

- 이 릴리스에서 [확장 호스트](azure-stack-extension-host-prepare.md) 사용 가능 합니다. 확장 호스트 네트워크 통합을 단순화 하 고 Azure Stack의 보안 태세를 향상 시킵니다.

- Active Directory Federated Services (AD FS)를 사용 하 여 장치 인증에 대 한 지원을 추가 합니다. 특히 Azure CLI를 사용 하는 경우. 자세한 내용은 참조 하세요. [Azure Stack에서 Azure CLI를 사용 하 여 사용 하 여 API 버전 프로필](./user/azure-stack-version-profiles-azurecli2.md)

- 클라이언트 암호를 사용 하 여 Active Directory Federated Services (AD FS)를 사용 하 여 서비스 주체에 대 한 지원이 추가 되었습니다. 자세한 내용은 [AD FS에 대 한 서비스 주체 만들기](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs)합니다.

- 이 릴리스에서 다음 Azure Storage 서비스 API 버전에 대 한 지원이 추가 되었습니다. **2017-07-29**하십시오 **2017-11-09**합니다. 다음 Azure Storage 리소스 공급자 API 버전에 대 한 지원도 추가 됩니다. **2016-05-01**, **2016-12-01**합니다 **2017-06-01**, 및 **2017-10-01**합니다. 자세한 내용은 참조 하세요. [Azure Stack 저장소: 차이점 및 고려 사항](./user/azure-stack-acs-differences.md)합니다.

- 새 privileged 끝점 명령 추가 업데이트 하 고 ADFS에 대 한 서비스 주체를 제거 합니다. 자세한 내용은 [AD FS에 대 한 서비스 주체 만들기](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs)합니다.

- 를 시작 하려면 Azure Stack 운영자를 허용 하는 추가 된 새 배율 단위 노드 작업 중지 및 크기 조정 단위 노드를 종료 합니다. 자세한 내용은 [Azure Stack에서 크기 조정 단위 노드 작업](azure-stack-node-actions.md)합니다.

- 환경의 등록 세부 정보를 표시 하는 새 영역 속성 블레이드를 추가 합니다. 클릭 하 여이 정보를 볼 수는 **하위 지역 관리** 관리자 포털에서 기본 대시보드에서 타일을 선택한 다음 **속성**합니다.

- 사용자 이름과 암호를 물리적 컴퓨터와 통신 하는 데 사용 하 여 BMC 자격 증명을 업데이트 하는 새 권한 있는 끝점 명령을 추가 합니다. 자세한 내용은 [베이스 보드 관리 컨트롤러를 업데이트할 \(BMC) 자격 증명](azure-stack-rotate-secrets.md)합니다.

- Azure portal에서 사용할 수 있기 방식과 유사 하 게 관리자 및 사용자 포털의 오른쪽 위 모서리에서 Azure 로드맵을 통해 액세스할 수 있도록 도움말 및 지원 아이콘 (물음표)을 추가 합니다.

- 연결이 끊긴된 사용자의 환경 향상 된 Marketplace 관리를 추가 합니다. 연결이 끊어진된 환경에서 마켓플레이스 항목을 게시 하는 업로드 프로세스는 이미지 및 Marketplace 패키지를 개별적으로 업로드 하는 대신 하나의 단계로 간소화 됩니다. 업로드 된 제품 Marketplace 관리 블레이드에 표시 됩니다. 자세한 내용은 [이 섹션](azure-stack-download-azure-marketplace-item.md#import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher)을 참조하세요. 

- 이 릴리스에서 외부 인증서만 하는 동안 회전 하는 기능을 추가 하 여 비밀 회전에 대 한 필요한 유지 관리 기간을 줄입니다 [Azure Stack 비밀 회전](azure-stack-rotate-secrets.md)합니다.

- [Azure Stack PowerShell](azure-stack-powershell-install.md) 버전 1.6.0으로 업데이트 되었습니다. 업데이트에는 Azure Stack에서 새로운 저장소와 관련 된 기능에 대 한 지원이 포함 됩니다. 자세한 내용은 참조에 대 한 릴리스 정보는 [Azure Stack 관리 모듈이 PowerShell 갤러리의 1.6.0](https://www.powershellgallery.com/packages/AzureStack/1.6.0) 업데이트 또는 Azure Stack PowerShell 설치에 대 한 정보를 참조 하세요. [용 PowerShell 설치 Azure Stack](azure-stack-powershell-install.md)합니다.

- 이제 managed Disks는 Azure Stack 포털을 사용 하 여 가상 컴퓨터를 만들 때 기본적으로 활성화 됩니다. 참조 된 [알려진 문제](#known-issues-post-installation) VM 생성 오류를 방지 하려면 Managed Disks에 필요한 추가 단계에 대 한 섹션입니다.

- 이 릴리스에서 경고를 소개 **복구** Azure Stack 운영자에 대 한 작업입니다. 일부 경고 1811에서 제공을 **복구** 문제를 해결 하려면 선택할 수 있는 경고의 단추입니다. 자세한 내용은 [Azure Stack의 상태 및 경고 모니터링](azure-stack-monitor-health.md)합니다.

## <a name="fixed-issues"></a>해결된 문제

<!-- TBD - IS ASDK --> 
- 공용 IP 주소 사용 측정기 데이터 알아보았습니다 동일한 문제를 해결 했습니다 **EventDateTime** 대신 각 레코드에 대 한 값을 **TimeDate** 레코드를 만든 경우를 보여 주는 스탬프입니다. 이제 공용 IP 주소 사용을 정확 하 게 설명 하는 데이 데이터를 사용할 수 있습니다.

<!-- 3099544 – IS, ASDK --> 
- Azure Stack 포털을 사용 하 여 새 가상 컴퓨터 (VM)를 만들 때 발생 한 문제를 해결 합니다. 발생 한 VM 크기를 선택 하는 **u s D/월** 표시할 열을 **사용할 수 없습니다** 메시지. 이 열이 더 이상 나타나지 않습니다. 표시 된 VM 가격 책정 열에서에서 지원 되지 않습니다 Azure Stack.

<!-- 2930718 - IS ASDK --> 
- 문제를 해결 했습니다. 사용자 구독 하는 경우의 세부 정보 블레이드를 닫고 클릭 한 후에 액세스할 때 관리자 포털 **최근**, 사용자 구독 이름이 표시 되지 않았습니다. 사용자 구독 이름에는 이제 다음이 표시 됩니다.

<!-- 3060156 - IS ASDK --> 
- 관리자와 사용자 포털에서 문제를 해결: 포털 설정을 클릭 하 고 선택 **모든 설정 및 개인 대시보드 삭제** 오류 알림이 표시 된 및 예상 대로 작동 하지 않았습니다. 이 옵션을 이제 제대로 작동합니다.

<!-- 2930799 - IS ASDK --> 
- 관리자와 사용자 포털에서 문제를 해결: 아래의 **모든 서비스**, 자산 **DDoS 보호 계획** 올바르게 나열 된 합니다. Azure Stack에서 사용할 수 없는 합니다. 목록 제거 되었습니다.
 
<!--2760466 – IS  ASDK --> 
- 새 Azure Stack 환경을 설치한 경우에 발생 한 문제를 해결 하는 경고를 나타냅니다 **활성화 필요** 표시 하지 않고 합니다. 이제 올바르게 표시 됩니다.

<!--1236441 – IS  ASDK --> 
- ADFS를 사용 하는 경우 RBAC 정책을 사용자 그룹에 적용 되지 않는 문제를 해결 합니다.

<!--3463840 - IS, ASDK --> 
- 공용 VIP 네트워크에서 액세스할 수 없는 파일 서버를 인해 실패 하는 인프라 백업 관련 문제를 해결 합니다. 이 문제가 해결 공개 인프라 네트워크에 인프라 백업 서비스를 다시 이동합니다. 최신을 적용 하는 경우 [1809에 대 한 Azure Stack 핫픽스](#azure-stack-hotfixes) 이 문제를 해결 하는, 1811 업데이트가 모든 추가로 수정할 수 없습니다. 

<!-- 2967387 – IS, ASDK --> 
- Azure Stack 관리자 또는 사용자 포털에 로그인 할 때 사용한 계정으로 표시 되는 문제를 해결 했습니다 **알 수 없는 사용자**합니다. 계정이 하나를 갖고 있지 않은 경우이 메시지가 표시 된를 **첫 번째** 또는 **마지막** 이름을 지정 합니다.   

<!--  2873083 - IS ASDK --> 
- 인해 가상 머신 확장 집합 (VMSS)를 만들려면 포털을 사용 하는 문제를 해결 합니다 **인스턴스 크기** 드롭다운을 Internet Explorer를 사용 하는 경우에 제대로 로드 되지 합니다. 이 브라우저는 이제 올바르게 작동합니다.  

<!-- 3190553 - IS ASDK -->
- 인프라 역할 인스턴스를 사용할 수 없습니다 또는 배율 단위 노드가 오프 라인 상태 였는 나타내는 불필요 한 경고를 생성 하는 문제가 수정 되었습니다.

## <a name="changes"></a>변경 내용

- 보고 계획의 할당량을 편집 하는 새로운 방법은 1811에서 도입 되었습니다. 자세한 내용은 [기존 할당량을 보려면](azure-stack-quota-types.md#view-an-existing-quota)합니다.

<!-- 3083238 IS -->
- 이 업데이트에 향상 된 보안 기능 디렉터리 서비스 역할의 백업 크기가 증가 하는 발생합니다. 외부 저장소 위치에 대 한 지침을 크기 조정 업데이트에 대 한 참조를 [인프라 backup 설명서](azure-stack-backup-reference.md#storage-location-sizing)합니다. 이 변경으로 인해 더 큰 크기 데이터 전송으로 인해 백업을 완료 하려면 시간이 더 길어집니다. 이 변경에 통합된 시스템에 영향을 줍니다. 

- Get-AzsCsvsRecoveryKeys Get AzsRecoveryKeys 하에서 1811의 BitLocker 복구 키를 검색 하려면 기존 PEP cmdlet 이름이 바뀝니다. BitLocker 복구 키를 검색 하는 방법에 대 한 자세한 내용은 참조 하세요. [키를 검색 하는 방법에 대 한 지침](azure-stack-security-bitlocker.md)합니다.

## <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures

이 업데이트에는 다음 보안 업데이트를 설치합니다.  

- [CVE-2018-8256](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8256)
- [CVE-2018-8407](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8407)
- [CVE-2018-8408](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8408)
- [CVE-2018-8415](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8415)
- [CVE-2018-8417](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8417)
- [CVE-2018-8450](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8450)
- [CVE-2018-8471](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8471)
- [CVE-2018-8476](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8476)
- [CVE-2018-8485](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8485)
- [CVE-2018-8544](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8544)
- [CVE-2018-8547](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8547)
- [CVE-2018-8549](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8549)
- [CVE-2018-8550](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8550)
- [CVE-2018-8553](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8553)
- [CVE-2018-8561](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8561)
- [CVE-2018-8562](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8562)
- [CVE-2018-8565](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8565)
- [CVE-2018-8566](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8566)
- [CVE-2018-8584](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8584)


이러한 취약점에 대 한 자세한 내용은 위의 링크를 클릭 하거나 Microsoft 기술 자료 문서를 참조 하세요 [4467684](https://support.microsoft.com/help/4467684)합니다.

## <a name="known-issues-with-the-update-process"></a>업데이트 프로세스를 사용 하 여 알려진된 문제

- 1811 설치 중 업데이트를이 시간 동안 관리자 포털의 모든 인스턴스를 닫았는지 확인 해야 합니다. 사용자 포털을 열어둘 수 있지만 관리 포털을 종료 해야 합니다.

- 실행 하는 경우 [테스트 AzureStack](azure-stack-diagnostic-test.md)경우는 **AzsInfraRoleSummary** 또는 **AzsPortalApiSummary** 테스트가 실패, 실행 하는 메시지가  **테스트 AzureStack** 사용 하 여는 `-Repair` 플래그입니다.  이 명령을 실행 하는 경우 다음 오류 메시지와 함께 실패 합니다.  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`  이 문제는 향후 릴리스에서 수정 될 예정입니다.

- 1811 업데이트를 설치 하는 동안는 Azure Stack 포털을 사용 하 여 사용할 수 없는 경우 확장 호스트를 구성 하는 동안 확장 호스트의 구성에는 최대 5 시간까지 걸릴 수 있습니다. 이 시간 동안 업데이트 상태를 확인하거나, [Azure Stack 관리자 PowerShell 또는 권한 있는 엔드포인트](azure-stack-monitor-update.md)를 사용하여 실패한 업데이트 설치를 다시 시작할 수 있습니다. 

- 1811 업데이트를 설치 하는 동안 사용자 포털 대시보드를 사용할 수 있습니다 하 고 사용자 지정이 손실 될 수 있습니다. 포털 설정을 열고 선택 하 여 업데이트가 완료 된 후 기본 설정으로 대시보드를 복원할 수 있습니다 **기본 설정 복원**합니다.

- 실행할 때 [테스트 AzureStack](azure-stack-diagnostic-test.md), 베이스 보드 관리 컨트롤러 (BMC)에서 경고 메시지가 표시 됩니다. 이 경고를 안전 하 게 무시할 수 있습니다.

- <!-- 2468613 - IS --> 이 업데이트를 설치 하는 동안 경고 제목으로 표시 될 수 있습니다 `Error – Template for FaultType UserAccounts.New is missing.` 이러한 경고를 안전 하 게 무시할 수 있습니다. 이 업데이트의 설치가 완료 된 후 경고를 자동으로 닫습니다.

- <!-- 3139614 | IS --> Azure Stack에 OEM에 게에서 업데이트를 적용 한 경우는 **사용할 수 있는 업데이트** 알림 Azure Stack 관리자 포털에 나타나지 않을 수 있습니다. Microsoft 업데이트를 설치 하려면 다운로드 하 고 여기에 있는 지침을 사용 하 여 수동으로 가져와서 [Azure Stack의 업데이트 적용](azure-stack-apply-updates.md)합니다.

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

<!-- TBD - IS ASDK --> 
- 분리 된 리소스에서 사용자 구독 결과 삭제합니다. 대 안으로 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

<!-- TBD - IS ASDK --> 
- Azure Stack 포털을 사용 하 여 구독에 사용 권한을 확인할 수 없습니다. 대 안으로 사용 하 여 [사용 권한을 확인 하려면 PowerShell](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan)합니다.

### <a name="health-and-monitoring"></a>상태 및 모니터링

<!-- 1264761 - IS ASDK -->  
- 에 대 한 경고를 표시 될 수 있습니다 합니다 **상태 컨트롤러** 다음 세부 정보는 구성 요소:  

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

    두 가지 경고는 무시 해도 됩니다. 이러한 작업은 시간이 지남에 따라 자동으로 종료 됩니다.  

### <a name="compute"></a>컴퓨팅

- 새 Windows 가상 머신 (VM)을 만들 때 합니다 **설정을** 블레이드에서는 계속 하려면 공용 인바운드 포트를 선택 해야 합니다. 1811이이 설정은 필요한 경우 영향을 미치지 않습니다. 즉, Azure Stack에서 구현 되지 않은 Azure 방화벽에서 기능에 따라 달라 집니다. 선택할 수 있습니다 **공용 인바운드 포트 없음**, 또는 VM 만들기를 진행 하는 다른 옵션 중 하나입니다. 설정을 아무런 효과가 없습니다.

- 새 Windows 가상 머신 (VM)를 만들 때 다음 오류가 표시 될 수 있습니다.

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   오류는 VM에서 부팅 진단을 사용 하도록 설정 해도 부트 진단 저장소 계정을 삭제 하는 경우에 발생 합니다. 이 문제를 해결 하려면 이전에 사용한 동일한 이름 사용 하 여 저장소 계정을 다시 만듭니다.

<!-- 3235634 – IS, ASDK -->
- 포함 된 크기를 사용 하 여 Vm을 배포 하는 **v2** 접미사; 예를 들어 **Standard_A2_v2**,으로 접미사를 지정할 **Standard_A2_v2** (소문자 v). 사용 하지 마세요 **Standard_A2_V2** (V 대문자). 이 글로벌 Azure에서 작동 하며 Azure Stack에서 일치 하지 않습니다.

<!-- 2869209 – IS, ASDK --> 
- 사용 하는 경우는 [ **추가 AzsPlatformImage** cmdlet](/powershell/module/azs.compute.admin/add-azsplatformimage)를 사용 해야 합니다는 **-OsUri** storage 계정과 디스크 업로드 되는 URI 매개 변수입니다. 디스크의 로컬 경로 사용 하는 경우 cmdlet은 다음 오류로 인해 실패 합니다. 

    `Long running operation failed with status 'Failed'`

<!--  2795678 – IS, ASDK --> 
- 가상 머신 (Vm)를 만들려면 포털 (DS, Ds_v2, FS, FSv2) 프리미엄 VM 크기에서를 사용 하면 표준 저장소 계정에 VM이 생성 됩니다. 표준 저장소 계정 만드는 영향을 주지 않습니다 기능적으로 IOPs 또는 대금 청구 합니다. 안전 하 게 되었다는 경고를 무시할 수 있습니다. 

    `You've chosen to use a standard disk on a size that supports premium disks. This could impact operating system performance and is not recommended. Consider using premium storage (SSD) instead.`

<!-- 2967447 - IS, ASDK --> 
- 가상 머신 확장 집합 (VMSS) 생성 환경에 대 한 배포 옵션으로 7.2 CentOS 기반을 제공합니다. 모두 배포에 대 한 다른 운영 체제를 선택 하거나 다운로드 하 여 marketplace에서 배포 하기 전에 다른 CentOS 이미지를 지정 하는 Azure Resource Manager 템플릿을 사용 하 여 해당 이미지를 Azure Stack에서 사용할 수 없는 때문에 연산자입니다.  

<!-- 2724873 - IS --> 
- PowerShell cmdlet을 사용 하는 경우 **시작 AzsScaleUnitNode** 하거나 **중지 AzsScaleunitNode** 배율 단위를 관리 하려면 시작 또는 중지 확장 단위를 첫 번째 시도가 실패할 수 있습니다. 첫 번째 실행에 실패 하면 cmdlet은 cmdlet을 두 번 실행 합니다. 두 번째 실행 작업을 성공적으로 완료 해야 합니다. 

<!-- TBD - IS ASDK --> 
- VM 배포에 확장을 프로 비전 너무 오래 걸리는 경우에 할당 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 대신 프로 비전 제한 시간을 사용 수 있습니다.  

<!-- 1662991 IS ASDK --> 
- Azure Stack의 Linux VM 진단을 지원 되지 않습니다. 를 사용 하도록 설정 하는 VM 진단을 사용 하 여 Linux VM을 배포 하는 경우 배포가 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정한 경우에 배포가 실패 합니다.  

<!-- 2724961- IS ASDK --> 
- 등록 하는 경우는 **Microsoft.Insight** 리소스 공급자 구독 설정에 게스트 OS 진단 사용 하도록 설정 된 Windows VM 만들기, VM 개요 페이지에서 CPU 백분율 차트에 메트릭 데이터를 표시 하지 않습니다.

   VM에 대 한 CPU 백분율 차트와 같은 메트릭 데이터를 찾으려면에서로 이동 합니다 **메트릭을** 창과 표시 지원 되는 모든 Windows VM 게스트 메트릭을 합니다.

<!-- 3507629 - IS, ASDK --> 
- Managed Disks에서는 두 개의 새 [계산 할당량 형식](azure-stack-quota-types.md#compute-quota-types) 프로 비전 할 수 있는 관리 되는 디스크의 최대 용량을 제한 합니다. 기본적으로 각 관리 되는 디스크 할당량 형식에 대 한 2048 GiB 할당 됩니다. 그러나 다음과 같은 문제가 발생할 수 있습니다.

   - 할당량의 1808 업데이트 이전에 만든 경우 Managed Disks 할당량에에서 표시 됩니다 0 값 관리자 포털을 2048 GiB 할당 됩니다. 늘리거나 실제 요구 사항 및 새로 설정에 따라 값을 줄일 수 할당량 값 2048 GiB 기본값을 재정의 합니다.
   - 0으로 할당량 값을 업데이트 하는 경우 기본값인 2048 GiB 같습니다. 대 안으로 할당량 값을 1로 설정 합니다.

<!-- TBD - IS ASDK --> 
- 업데이트 된 1811를 적용 한 후, Managed Disks를 사용 하 여 Vm을 배포 하는 경우 다음과 같은 문제가 발생할 수 있습니다.

   - Managed Disks를 사용 하 여 VM 배포 1808 업데이트 하기 전에 구독을 만든 경우 내부 오류 메시지와 함께 실패할 수 있습니다. 오류를 해결 하려면 각 구독에 대해 다음이 단계를 수행 합니다.
      1. 테 넌 트 포털에서로 이동 **구독** 구독을 찾습니다. 선택 **리소스 공급자**을 선택한 후 **Microsoft.Compute**를 클릭 하 고 **re-register**합니다.
      2. 로 동일한 구독에 따라 **액세스 제어 (IAM)**, 되어 있는지 확인 하 고 **Azure Stack – Managed Disk** 나열 됩니다.
   - 다중 테 넌 트 환경에 구성한 게스트 디렉터리와 연결 된 구독에서 Vm을 배포할 내부 오류 메시지와 함께 실패할 수 있습니다. 오류를 해결 하려면에서 다음이 단계를 수행 [이 문서에서는](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) 각 게스트 디렉터리를 다시 구성 합니다.

- 사용 하도록 설정 하는 SSH 인증을 사용 하 여 만든 Ubuntu 18.04 VM은 SSH 키를 사용 하 여 로그인 할 수 없습니다. 이 문제를 해결 하려면 Linux 확장에 대 한 VM 액세스 프로 비전 한 후 SSH 키를 구현 하는 데 또는 암호 기반 인증을 사용 합니다.

### <a name="networking"></a>네트워킹  

<!-- 1766332 - IS ASDK --> 
- 아래 **네트워킹**를 클릭 하면 **VPN 게이트웨이 만들기** VPN 연결을 설정 하려면 **정책 기반** VPN 형식으로 나열 됩니다. 이 옵션을 선택하지 마세요. 만 **경로 기반** 옵션은 Azure Stack에서 지원 됩니다.

<!-- 1902460 - IS ASDK --> 
- Azure Stack 지원 단일 *로컬 네트워크 게이트웨이* IP 주소당 합니다. 모든 테 넌 트 구독에서 그렇습니다. 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만드는 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려는 시도가 거부 됩니다.

<!-- 16309153 - IS ASDK --> 
- DNS 서버 설정을 사용 하 여 만든 가상 네트워크 **자동**, 사용자 지정 DNS 서버에 변경에 실패 합니다. 업데이트 된 설정은 해당 Vnet의 Vm에 푸시되 지 않습니다.

<!-- 2529607 - IS ASDK --> 
- Azure Stack 하는 동안 *암호 회전*에는 공용 IP 주소에 연결할 수 없는 2 ~ 5 분에 대 한 간격이 있습니다.

<!-- 2664148 - IS ASDK --> 
-   테 넌 트 S2S VPN 터널을 사용 하 여 virtual machines를 액세스는 시나리오에서는 온-프레미스 서브넷이 게이트웨이 이미 만든 후 로컬 네트워크 게이트웨이를 추가한 경우 연결 시도가 실패 하는 있는 경우를 발생할 수 있습니다. 

- Azure Stack 포털에 VM 인스턴스에 연결 된 네트워크 어댑터에 바인딩되어 있는 IP 구성에 대 한 고정 IP 주소를 변경 하면 나타납니다 없다는 경고 메시지 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`. 

    이 메시지를 안전 하 게 무시할 수 있습니다. IP 주소는 VM 인스턴스 다시 시작 하지 않습니다 하는 경우에 변경 됩니다.

- 포털에서에 **네트워킹 속성** 에 대 한 링크가 블레이드 **효과적인 보안 규칙** 각 네트워크 어댑터에 대 한 합니다. 이 링크를 선택 하면 새 블레이드가 열리고 오류 메시지를 보여 주는 `Not Found.` Azure Stack에서 아직 지원 하지 않으므로이 오류는 발생 **효과적인 보안 규칙**합니다.

- 인바운드 보안 규칙을 추가 하 고 선택 하는 경우 포털에서 **서비스 태그** 원본으로 여러 옵션에 표시 되는 **소스 태그가** Azure Stack에 대해 사용할 수 없는 목록입니다. Azure Stack에서 사용할 수 있는 유일한 옵션 아래와 같습니다.

    - **인터넷**
    - **VirtualNetwork**
    - **AzureLoadBalancer**
  
    다른 옵션으로 Azure Stack에서 소스 태그는 지원 되지 않습니다. 마찬가지로, 아웃 바운드 보안 규칙을 추가 하 고 선택 하는 경우 **서비스 태그** 옵션에 대 한 동일한 목록 대상으로 **소스 태그가** 표시 됩니다. 유효한 옵션은 동일 **소스 태그가**이전 목록에 설명 된 대로 합니다.

- 합니다 **새로 만들기-AzureRmIpSecPolicy** PowerShell cmdlet 설정을 지원 하지 않습니다 **DHGroup24** 에 대 한는 `DHGroup` 매개 변수입니다.

### <a name="infrastructure-backup"></a>인프라 백업

<!--scheduler config lost, bug 3615401, new issue in 1811,  hectorl-->
<!-- TSG: https://www.csssupportwiki.com/index.php/Azure_Stack/KI/Backup_scheduler_configuration_lost --> 
- 자동 백업, 사용 하도록 설정한 후 scheduler 서비스 상태가 비활성 예기치 않게 됩니다. 백업 컨트롤러 서비스는 자동 백업 사용 되지 않으며 관리자 포털에서 경고는 감지 합니다. 이 경고는 자동 백업 비활성화 되 면 예상 됩니다. 
    - 원인: 이 문제는 스케줄러 구성의 손실 되는 서비스의 버그 때문입니다. 이 버그는 저장소 위치, 사용자 이름, 암호 또는 암호화 키 변경 되지 않습니다.   
    - 업데이트 관리: 이 문제를 완화 하려면 인프라 백업 리소스 공급자에서 백업 컨트롤러 설정 블레이드를 열고 선택 **자동 백업 사용**합니다. 원하는 빈도 및 보존 기간을 설정 해야 합니다.
    - 발생 빈도: 낮음 

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- 구독에서 첫 번째 Azure Function을 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>업데이트 다운로드

Azure Stack 1811 업데이트 패키지를 다운로드할 수 있습니다 [여기](https://aka.ms/azurestackupdatedownload)합니다. 

연결 된 시나리오 에서만 Azure Stack 배포 주기적으로 보안된 끝점을 확인 하 고 자동으로 클라우드를 위한 사용 가능한 업데이트가 있는 경우을 알립니다. 자세한 내용은 [Azure Stack에 대 한 업데이트 관리](azure-stack-updates.md#using-the-update-tile-to-manage-updates)합니다.

## <a name="next-steps"></a>다음 단계

- Azure Stack 통합 시스템 및 지원 되는 상태 시스템을 유지 하기 위해 해야 할 새로운 서비스의 정책을 참조 하세요 [Azure Stack 서비스 정책](azure-stack-servicing-policy.md)합니다.  
- 권한 있는 끝점 (PEP)를 사용 하 여 모니터링 하 고 업데이트를 다시 시작, 참조 [권한 있는 끝점을 사용 하 여 Azure Stack의 업데이트 모니터링](azure-stack-monitor-update.md)합니다.  
- Azure Stack의 업데이트 관리 개요를 참조 하세요 [Azure Stack 개요에 대 한 업데이트 관리](azure-stack-updates.md)합니다.  
- Azure Stack을 사용 하 여 업데이트를 적용 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Stack의 업데이트 적용](azure-stack-apply-updates.md)합니다.  
