---
title: Windows 가상 데스크톱 안전 URL 목록-Azure
description: Windows 가상 데스크톱 배포가 의도 한 대로 작동 하도록 차단 해야 하는 Url 목록입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 90db861a4ef4fc951844d3ae82a51d20cf9dc8c5
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875107"
---
# <a name="safe-url-list"></a>안전 URL 목록

Windows 가상 데스크톱 배포가 제대로 작동 하도록 특정 Url을 차단 해제 해야 합니다. 이 문서에서는 이러한 Url을 나열 하므로 안전 하 게 보호 되는 항목을 알 수 있습니다.

## <a name="virtual-machines"></a>가상 머신

Windows 가상 데스크톱에 대해 만든 Azure virtual machines는 Azure 상용 클라우드의 다음 Url에 대 한 액세스 권한이 있어야 합니다.

|주소|아웃바운드 TCP 포트|목적|서비스 태그|
|---|---|---|---|
|*.wvd.microsoft.com|443|서비스 트래픽|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|에이전트 트래픽|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|에이전트 트래픽|AzureCloud|
|* xt.blob.core.windows.net|443|에이전트 트래픽|AzureCloud|
|* eh.servicebus.windows.net|443|에이전트 트래픽|AzureCloud|
|* xt.table.core.windows.net|443|에이전트 트래픽|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Windows 정품 인증|인터넷|
|mrsglobalsteus2prod.blob.core.windows.net|443|에이전트 및 SXS 스택 업데이트|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Azure Portal 지원|AzureCloud|
| 169.254.169.254 | 80 | [Azure Instance Metadata service 끝점](../virtual-machines/windows/instance-metadata-service.md) | N/A |
| 168.63.129.16 | 80 | [세션 호스트 상태 모니터링](../virtual-network/security-overview.md#azure-platform-considerations) | N/A |

>[!IMPORTANT]
>이제 Windows Virtual Desktop에서 FQDN 태그를 지원합니다. 자세한 내용은 [Azure Firewall을 사용하여 Windows Virtual Desktop 배포 보호](../firewall/protect-windows-virtual-desktop.md)를 참조하세요.
>
>서비스 문제를 방지하기 위해 URL 대신 FQDN 태그를 사용하는 것이 좋습니다. 나열된 URL과 태그는 Windows Virtual Desktop 사이트 및 리소스에만 해당됩니다. Azure Active Directory와 같은 다른 서비스에 대한 URL은 포함되지 않습니다.

Windows 가상 데스크톱에 대해 만든 Azure virtual machines는 Azure Government 클라우드의 다음 Url에 액세스할 수 있어야 합니다.

|주소|아웃바운드 TCP 포트|목적|서비스 태그|
|---|---|---|---|
|*. wvd.microsoft.us|443|서비스 트래픽|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|에이전트 트래픽|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|에이전트 트래픽|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|에이전트 트래픽|AzureCloud|
|* xt.blob.core.usgovcloudapi.net|443|에이전트 트래픽|AzureCloud|
|*.servicebus.usgovcloudapi.net|443|에이전트 트래픽|AzureCloud|
|* xt.table.core.usgovcloudapi.net|443|에이전트 트래픽|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Windows 정품 인증|인터넷|
|mrsglobalstugviffx.core.usgovcloudapi.net|443|에이전트 및 SXS 스택 업데이트|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Azure Portal 지원|AzureCloud|
| 169.254.169.254 | 80 | [Azure Instance Metadata service 끝점](../virtual-machines/windows/instance-metadata-service.md) | N/A |
| 168.63.129.16 | 80 | [세션 호스트 상태 모니터링](../virtual-network/security-overview.md#azure-platform-considerations) | N/A |

다음 표에는 Azure 가상 머신에서 액세스할 수 있는 선택적 URL이 나열되어 있습니다.

|주소|아웃바운드 TCP 포트|목적|Azure .Gov|
|---|---|---|---|
|*.microsoftonline.com|443|Microsoft Online Services에 대한 인증|login.microsoftonline.us|
|*.events.data.microsoft.com|443|원격 분석 서비스|None|
|www.msftconnecttest.com|443|OS가 인터넷에 연결되어 있는지 검색합니다.|None|
|*.prod.do.dsp.mp.microsoft.com|443|Windows 업데이트|None|
|login.windows.net|443|Microsoft Online Services, Microsoft 365에 로그인|login.microsoftonline.us|
|*.sfx.ms|443|OneDrive 클라이언트 소프트웨어에 대한 업데이트|oneclient.sfx.ms|
|*.digicert.com|443|인증서 해지 확인|None|

>[!NOTE]
>현재 Windows 가상 데스크톱에는 네트워크 트래픽을 허용 하기 위해 차단 해제할 수 있는 IP 주소 범위 목록이 없습니다. 지금은 특정 Url의 차단 해제만 지원 합니다.
>
>필수 Azure Active Directory 관련 Url을 포함 하 여 안전한 Office 관련 Url 목록은 [office 365 url 및 IP 주소 범위](/office365/enterprise/urls-and-ip-address-ranges)를 참조 하세요.
>
>서비스 트래픽과 관련된 URL에는 와일드카드 문자(*)를 사용해야 합니다. 에이전트 관련 트래픽에 *를 사용하지 않으려는 경우 와일드 카드 없이 URL을 찾는 방법은 다음과 같습니다.
>
>1. Windows Virtual Desktop 호스트 풀에 가상 머신을 등록합니다.
>2. **이벤트 뷰어**를 열고 **Windows 로그**  >  **응용 프로그램**  >  **wvd-에이전트로** 이동 하 여 이벤트 ID 3701을 찾습니다.
>3. 이벤트 ID 3701에서 찾은 Url의 차단을 해제 합니다. 이벤트 ID 3701 아래의 Url은 지역에 따라 다릅니다. 가상 컴퓨터를 배포 하려는 각 지역에 대 한 관련 Url을 사용 하 여 차단 해제 프로세스를 반복 해야 합니다.

## <a name="remote-desktop-clients"></a>원격 데스크톱 클라이언트

사용 하는 모든 원격 데스크톱 클라이언트에는 다음 Url에 대 한 액세스 권한이 있어야 합니다.

|주소|아웃바운드 TCP 포트|목적|클라이언트|Azure .Gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|서비스 트래픽|모두|*. wvd.microsoft.us|
|\*.servicebus.windows.net|443|데이터 문제 해결|모두|*.servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft FWLinks|모두|없음|
|aka.ms|443|Microsoft URL 단축기|모두|없음|
|docs.microsoft.com|443|설명서|모두|없음|
|privacy.microsoft.com|443|개인정보처리방침|모두|없음|
|query.prod.cms.rt.microsoft.com|443|클라이언트 업데이트|Windows Desktop|없음|

>[!IMPORTANT]
>이러한 URL을 여는 것은 신뢰할 수 있는 클라이언트 환경을 위해 필수적입니다. 이러한 URL에 대한 액세스를 차단하는 것은 지원되지 않으며 서비스 기능에 영향을 줍니다.
>
>이러한 Url은 클라이언트 사이트 및 리소스에만 해당 됩니다. 이 목록에는 Azure Active Directory와 같은 다른 서비스에 대 한 Url이 포함 되지 않습니다. Azure Active Directory Url은 [Office 365 url 및 IP 주소 범위](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)에서 ID 56에서 찾을 수 있습니다.
