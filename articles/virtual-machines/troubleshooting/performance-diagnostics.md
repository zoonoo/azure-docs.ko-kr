---
title: Azure 가상 머신의 성능 진단 | Microsoft Docs
description: Windows용 Azure Performance Diagnostics를 소개합니다.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: f79be44d33a327f3ead8d950885cb5d47ce3a015
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47412520"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Azure 가상 머신의 성능 진단

성능 진단 도구를 사용하면 Windows VM(가상 머신)에 영향을 줄 수 있는 성능 문제를 해결하는 데 도움이 됩니다. 지원되는 문제 해결 시나리오에는 알려진 문제 및 모범 사례 그리고 VM의 느린 성능이나 CPU, 디스크 공간 또는 메모리의 많은 사용량을 포함하는 복잡한 문제에 대한 빠른 검사가 포함됩니다. 

또한 다양한 로그, 풍부한 구성 및 진단 데이터에 대한 보고서 및 자세한 정보를 검토할 수 있는 경우 Azure Portal에서 직접 성능 진단을 실행할 수 있습니다. Microsoft 지원에 문의하기 전에 성능 진단을 실행하고 자세한 정보 및 진단 데이터를 검토하는 것이 좋습니다.

> [!NOTE]
> 성능 진단은 현재 .NET SDK 버전 4.5 이상이 설치된 Windows VM에서 지원됩니다. 클래식 VM에서 성능 진단을 실행하는 단계는 [Azure 성능 진단 VM 확장](performance-diagnostics-vm-extension.md)을 참조하세요.

### <a name="supported-operating-systems"></a>지원되는 운영 체제
Windows 10, Windows 8, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1, Windows Server 2016, Windows Server 2012, Windows Server 2012 Datacenter, Windows Server 2012 R2, Windows Server 2012 R2 Datacenter, Windows Server 2012 R2 Standard, Windows Server 2012 Standard, Windows Server 2008 R2, Windows Server 2008 R2 Datacenter, Windows Server 2008 R2 Enterprise, Windows Server 2008 R2 Foundation, Windows Server 2008 R2 SP1, Windows Server 2008 R2 Standard.

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>VM에 성능 진단을 설치 및 실행
성능 진단은 [PerfInsights](https://aka.ms/perfinsights)라는 진단 도구를 실행하는 VM 확장을 설치합니다. 성능 진단을 설치하고 실행하려면 다음과 같은 단계를 따릅니다.
1.  명령의 왼쪽 열에서 **가상 머신**을 선택합니다.
1.  VM 이름 목록에서 진단을 실행하려는 VM을 선택합니다.
1.  명령의 오른쪽 열에서 **성능 진단**을 선택합니다.

    ![성능 진단 설치 단추가 강조 표시된 Azure Portal의 스크린샷](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > 이 스크린샷에는 VM 이름의 블레이드가 숨겨져 있습니다.
1. 저장소 계정 선택(선택 사항)

    여러 VM에 대한 성능 진단 결과를 저장하기 위해 단일 저장소 계정을 사용하려는 경우 도구 모음에서 **설정** 단추를 클릭하여 저장소 계정을 선택할 수 있습니다. 저장소 계정을 선택하면 **확인** 단추를 클릭합니다.

    저장소 계정을 지정하지 않은 경우 기본적으로 새 저장소 계정이 만들어집니다.

    ![설정 도구 모음 단추가 강조 표시된 성능 진단 블레이드의 스크린샷](media/performance-diagnostics/settings-button.png)

    ![성능 진단 설정 블레이드에서 저장소 계정을 선택하는 스크린샷](media/performance-diagnostics/select-storage-account.png)

1. **성능 진단 설치** 단추를 선택합니다.
1. 설치가 완료된 후 진단을 실행하려면 **진단 실행** 확인란을 선택합니다. 이 확인란을 선택하면 성능 분석 시나리오 및 관련 옵션을 선택할 수 있습니다.

    ![성능 진단 설치 단추의 스크린샷](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>실행할 분석 시나리오를 선택

다음 분석 시나리오는 Azure Portal에서 사용할 수 있습니다. 발생하는 성능 문제에 따라 분석을 선택합니다. 분석의 필요에 따라 기간 및 추적 옵션을 선택합니다.

* **빠른 성능 분석**  
    알려진 문제를 확인하고, 모범 사례를 분석하고, 진단 데이터를 수집합니다. 이 분석을 실행하는 데 몇 분이 걸립니다. [자세히 알아보기](https://aka.ms/perfinsights/quick)

* **성능 분석**  
    빠른 성능 분석의 모든 검사를 포함하고 높은 리소스 사용량을 모니터링합니다. 이 버전을 사용하여 CPU, 메모리 및 디스크의 높은 사용량 등의 일반적인 성능 문제를 해결합니다. 이 분석은 선택한 기간에 따라 30초에서 15분 정도 걸립니다. [자세히 알아보기](https://aka.ms/perfinsights/vmslow) 
    
* **고급 성능 분석**  
    성능 분석의 모든 검사를 포함하고 다음 섹션에 나열된 것처럼 하나 이상의 추적을 수집합니다. 이 시나리오를 사용하여 추가 추적이 필요한 복잡한 문제를 해결합니다. 이 시나리오를 장기간 실행하면 VM의 크기 및 선택된 추적 옵션에 따라 진단 출력의 전체 크기가 증가합니다. 이 분석을 실행하는 데는 선택한 기간에 따라 30초에서 15분 정도 걸립니다. [자세히 알아보기](https://aka.ms/perfinsights/advanced) 
    
* **Azure Files 분석**  
    성능 분석의 모든 검사를 포함하고 네트워크 추적 및 SMB 카운터를 캡처합니다. 이 시나리오를 사용하여 Azure Files의 성능 문제를 해결합니다. 이 분석을 실행하는 데는 선택한 기간에 따라 30초에서 15분 정도 걸립니다. [자세히 알아보기](https://aka.ms/perfinsights/azurefiles)


![성능 진단 블레이드 내에서 진단 창을 실행하는 스크린 샷](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>증상 제공(선택 사항)
목록에서 미리 선택된 모든 증상을 선택하거나 새 증상을 추가합니다. 이렇게 하면 나중에 분석을 개선하는 데 유용합니다. 

### <a name="provide-support-request-number-if-available-optional"></a>가능한 경우 지원 요청 번호를 제공(선택 사항)
기존 지원 티켓에서 Microsoft 지원 엔지니어와 함께 작업하는 경우 지원 티켓 번호를 제공합니다. 

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>개인 정보 취급 방침 및 약관을 검토하고 확인란을 선택하여 승인(필수)
진단을 실행하려면 약관 및 개인 정보 취급 방침에 동의해야 합니다.

### <a name="select-ok-to-run-the-diagnostics"></a>확인을 선택하여 진단을 실행 
성능 진단이 설치를 시작하면 알림이 표시됩니다. 설치가 완료되면 성공적으로 설치되었음을 나타내는 알림이 표시됩니다. 그런 다음, 선택한 분석이 지정 기간 동안 실행됩니다. 이 기간은 정확한 시간에 진단 데이터를 캡처할 수 있도록 성능 문제를 재현하기에 적합합니다. 

분석이 완료되면 다음 항목을 Azure 테이블 및 지정된 저장소 계정의 BLOB(Binary Large Object) 컨테이너에 업로드합니다.

*   실행에 대한 모든 인사이트 및 관련 정보
*   로그 파일이 포함된 출력 압축(.zip) 파일(**PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip**)
*   HTML 보고서

업로드 후 Azure Portal에 새 진단 보고서가 나열됩니다.

![성능 진단 블레이드의 진단 보고서 목록의 스크린샷](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>성능 진단 설정을 변경하는 방법
**설정** 도구 모음 단추를 사용하여 진단 정보 및 출력을 저장할 수 있는 저장소 계정을 변경합니다. 성능 진단을 사용하는 여러 VM에 대해 동일한 저장소 계정을 사용할 수 있습니다. 저장소 계정을 변경하는 경우 이전 보고서 및 정보는 삭제되지 않습니다. 그러나 이전 보고서 및 정보는 진단 보고서 목록에 더 이상 표시되지 않습니다. 

## <a name="review-insights-and-performance-diagnostics-report"></a>인사이트 및 성능 진단 보고서 검토
각 진단 실행에는 오프라인 보기용 보고서 외에 인사이트 및 권장 사항, 영향을 받는 리소스, 로그 파일, 수집되는 기타 다양한 진단 정보의 목록이 포함됩니다. 모든 수집된 진단 데이터의 전체 목록은 [PerfInsights에서 수집하는 정보 유형은?](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/how-to-use-perfinsights#what-kind-of-information-is-collected-by-perfinsights)을 참조하세요. 

### <a name="select-a-performance-diagnostics-report"></a>성능 진단 보고서 선택
진단 보고서 목록을 사용하여 실행된 모든 진단 보고서를 찾을 수 있습니다. 목록에는 사용된 분석에 대한 세부 정보, 발견된 정보 및 해당 영향 수준이 포함됩니다. 행을 선택하여 자세한 내용을 봅니다.

![성능 진단 블레이드에서 진단 보고서를 선택하는 스크린샷](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>성능 진단 보고서 검토
각 성능 진단 보고서는 여러 인사이트를 포함하고 영향 수준을 높음, 중간 또는 낮음으로 나타낼 수 있습니다. 각 인사이트에는 걱정을 줄이는 데 도움이 되는 권장 사항이 포함되어 있습니다. 인사이트는 간단한 필터링을 위해 그룹화됩니다. 

영향 수준은 잘못된 구성, 알려진 문제 또는 다른 사용자가 보고한 문제 등의 요인에 따른 성능 문제의 가능성을 나타냅니다. 아직 나열된 문제 중 하나 이상의 문제가 발생하지 않을 수 있습니다. 예를 들어 동일한 데이터 디스크에 SQL 로그 파일 및 데이터베이스 파일이 있을 수 있습니다. 이 조건에서는 데이터베이스 사용량이 높은 경우 병목 상태 및 기타 성능 문제가 발생할 가능성이 높은 반면, 사용량이 낮으면 문제를 인식하지 못할 수 있습니다.

![성능 진단 보고서 개요 블레이드의 스크린샷](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>성능 진단 인사이트 및 권장 사항 검토
인사이트를 선택하여 영향을 받는 리소스, 제안된 해결 방법 및 참조 링크에 대한 자세한 내용을 볼 수 있습니다. 

![성능 진단 인사이트 세부 정보의 스크린샷](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>전체 성능 진단 보고서 다운로드 및 검토
**보고서 다운로드** 단추를 사용하여 저장소 및 네트워크 구성, 성능 카운터, 추적, 프로세스 목록 및 로그와 같은 다양한 추가 진단 정보가 포함된 HTML 보고서를 다운로드할 수 있습니다. 콘텐츠는 선택한 분석에 따라 달라집니다. 고급 문제 해결의 보고서에는 추가 정보 및 높은 CPU 사용량, 높은 디스크 사용량 및 과도한 메모리를 사용하는 프로세스에 관련된 대화형 차트가 포함될 수 있습니다. 성능 진단 보고서에 대한 자세한 내용은 [진단 보고서 검토](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/how-to-use-perfinsights#review-the-diagnostics-report)를 참조하세요.

## <a name="manage-performance-diagnostics-reports"></a>성능 진단 보고서 관리
**보고서 삭제** 단추를 사용하여 하나 이상의 성능 진단 보고서를 삭제할 수 있습니다.

## <a name="how-to-uninstall-performance-diagnostics"></a>성능 진단을 제거하는 방법
VM에서 성능 진단을 제거할 수 있습니다. 이 작업을 통하면 VM 확장이 제거되지만 저장소 계정에 있는 모든 진단 데이터가 영향을 받지는 않습니다. 

![제거 단추가 강조 표시된 성능 진단 블레이드 도구 모음의 스크린샷](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>내 VM의 진단 데이터는 어디에 저장되나요? 
모든 성능 진단 정보 및 보고서는 사용자 고유의 저장소 계정에 저장됩니다. 인사이트는 Azure 테이블 내에 저장됩니다. 보고서 압축 파일은 azdiagextnresults이라는 BLOB(Binary Large Object) 컨테이너에 저장됩니다.

도구 모음에서 설정 단추를 사용하여 저장소 계정 정보를 볼 수 있습니다. 

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Microsoft 고객 지원팀과 이 데이터를 공유하려면 어떻게 하나요? 
Microsoft와 진단 보고서를 공유하는 방법은 여러 가지가 있습니다.

**옵션 1:** 자동으로 최신 보고서를 공유  
Microsoft에서 지원 티켓을 여는 경우 성능 진단 보고서를 공유하는 것이 중요합니다. 진단을 실행하는 동안("**Microsoft와 진단 정보 공유에 동의합니다**" 확인란을 선택하여) Microsoft와 이 정보를 공유하려는 경우 Microsoft는 실행일로부터 최대 30일 동안 출력 Zip 파일에 대한 SAS 링크를 사용하여 저장소에서 보고서에 액세스할 수 있습니다. 지원 엔지니어에게는 최신 보고서만 제공됩니다. 

**옵션 2:** 진단 보고서 압축 파일에 대한 공유 액세스 서명 생성  
공유 액세스 서명을 사용하여 보고서 압축 파일에 대한 링크를 공유할 수 있습니다. 이렇게 하려면 다음 단계를 수행하세요. 
1.  Azure Portal에서 진단 데이터 저장되는 저장소 계정으로 이동합니다.
1.  **Blob 서비스** 섹션에서 **Blob**을 선택합니다. 
1.  **azdiagextnresults** 컨테이너를 선택합니다.
1.  공유하려는 성능 진단 출력 압축 파일을 선택합니다.
1.  **SAS 생성** 탭에서 공유 조건을 선택합니다. 
1.  **URL 및 Blob SAS 토큰 생성**을 클릭합니다.
1.  **Blob SAS URL**을 복사하여 지원 엔지니어와 공유합니다. 

**옵션 3:** 저장소 계정에서 보고서를 다운로드

또한 옵션 2의 1-4 단계를 사용하여 성능 진단 보고서 압축 파일을 찾을 수 있습니다. 파일의 다운로드를 선택한 다음, 이메일을 통해 파일을 공유하거나 파일의 업로드 지침을 지원 엔지니어에게 문의합니다.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>정확한 시간에 진단 데이터를 캡처하려면 어떻게 하나요?
각 성능 진단 실행마다 두 단계가 있습니다. 
1.  성능 진단 VM 확장을 설치하거나 업데이트합니다.
1.  지정된 기간에 진단을 실행합니다.

현재 VM 확장 설치가 언제 완료되는지 정확하게 알 수 있는 간단한 방법은 없습니다. 일반적으로 VM 확장을 설치하려면 약 45초에서 1분이 걸립니다. VM 확장을 설치한 후 재현 단계를 실행하여 성능 진단이 문제 해결을 위해 올바른 데이터 집합을 캡처하도록 할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
성능 진단 정보 및 보고서를 검토한 후에도 여전히 문제의 원인을 확인할 수 없고 도움이 더 필요한 경우 Microsoft 고객 지원팀에서 지원 티켓을 열 수 있습니다. 

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 **지원 받기**를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
