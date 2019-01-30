---
title: Azure Stack 서비스 정책 | Microsoft Docs
description: 정책 및 지원 되는 상태에서 통합된 시스템을 유지 하는 서비스는 Azure Stack에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 1664e19f3a238b2986af4669c3853a54d7c7e328
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250826"
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack 서비스 정책

이 문서에서는 Azure Stack 통합 시스템 및 지원 되는 상태 시스템을 유지 하기 위해 해야 할 항목에 대 한 서비스 정책을 설명 합니다.

## <a name="download-update-packages-for-integrated-systems"></a>통합된 시스템에 대 한 업데이트 패키지 다운로드

Microsoft는 전체 월간 업데이트 패키지 뿐만 아니라 특정 문제를 해결 하려면 핫픽스 패키지 모두 해제 합니다.

월간 업데이트 패키지는 보안 Azure 끝점에서 호스트 됩니다. 사용 하 여 수동으로 다운로드할 수 있습니다 합니다 [Azure Stack 업데이트 다운로더 도구](https://aka.ms/azurestackupdatedownload)합니다. 업데이트 관리자 포털에 자동으로 표시 하는 배율 단위에 연결 되어 있으면 **사용할 수 있는 업데이트**합니다. Full, 월간 업데이트 패키지 문서화 되어 각 릴리스마다 합니다. 각 릴리스에 대 한 자세한 정보에 대 한 모든 릴리스를 클릭할 수는 [업데이트 패키지에 대 한 릴리스 작업 리듬](#update-package-release-cadence) 이 문서의 섹션입니다.

핫픽스 업데이트 패키지는 동일한 보안 Azure 끝점에서 호스트 됩니다. 각각의 개별 핫픽스 KB 문서; 포함 된 링크를 사용 하 여 수동으로 다운로드할 수 있습니다. 예를 들어 [Azure Stack 핫픽스 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114)합니다. 전체, 월간 업데이트 패키지와 마찬가지로, Azure Stack 운영자.xml,.bin 및.exe 파일을 다운로드 하의 절차를 사용 하 여 가져올 [Azure Stack의 업데이트 적용](azure-stack-apply-updates.md)합니다. 연결 된 배율 단위를 사용 하 여 azure Stack 운영자는 메시지를 사용 하 여 관리자 포털에 자동으로 표시 하는 핫픽스 나타납니다 **사용할 수 있는 업데이트**합니다.

배율 단위에 연결 되어 있지 않습니다. 각 핫픽스 릴리스 하는 방법에 대 한 알림을 받을 것인지를 구독 하는 [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss … ) 또는 [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom …) 피드 각 릴리스에서 언급 합니다.  

## <a name="update-package-types"></a>업데이트 패키지 형식

통합된 시스템에 대 한 업데이트 패키지는 다음과 같은 두 종류가 있습니다.

- **Microsoft 소프트웨어 업데이트**합니다. Microsoft는 Microsoft 소프트웨어 업데이트 패키지에 대 한 종단 간 서비스 수명 주기를 담당 합니다. 이러한 패키지는 최신 Windows Server 보안 업데이트, 비보안 업데이트 및 Azure Stack 기능 업데이트에 포함 될 수 있습니다. Microsoft에서 직접 이러한 업데이트 패키지를 다운로드할 수 있습니다.

- **OEM 하드웨어 공급 업체에서 제공한 업데이트**합니다. Azure Stack 하드웨어 파트너 종단 간 서비스 수명 주기 (지침 포함)는 하드웨어 관련 펌웨어와 드라이버 업데이트 패키지에 대 한 책임이 있습니다. 또한 Azure Stack 하드웨어 파트너 소유 하 고 모든 소프트웨어 및 하드웨어 수명 주기 호스트의 하드웨어에 대 한 지침을 유지 관리 합니다. 이러한 OEM 하드웨어 공급 업체 호스트 자체 다운로드 사이트에서 패키지를 업데이트 합니다.

## <a name="update-package-release-cadence"></a>업데이트 패키지에 대 한 릴리스 작업 리듬

Microsoft는 매월 주기로 소프트웨어 업데이트 패키지를 해제 하려면 필요 합니다. 그러나 한 달에 없거나 여러 업데이트 릴리스를 가질 수 것입니다. OEM 하드웨어 공급 업체는 필요에 따라 해당 업데이트를 릴리스 합니다.

현재 버전을 확인 하는 방법에 대 한 계획 및 업데이트를 관리 하는 방법에 대 한 설명서 [관리 업데이트 개요](azure-stack-updates.md)합니다.

를 다운로드 하는 방법을 비롯 한 특정 업데이트에 대 한 정보 업데이트에 대 한 릴리스 정보를 참조 하세요.

- [Azure Stack 1811 업데이트](azure-stack-update-1811.md)
- [Azure Stack 1809 업데이트](azure-stack-update-1809.md)
- [Azure Stack 1808 업데이트](azure-stack-update-1808.md)
- [Azure Stack 1807 업데이트](azure-stack-update-1807.md)

## <a name="hotfixes"></a>핫픽스

경우에 따라 Microsoft는 핫픽스 Azure Stack에 대 한 해당 주소는 예방 또는 시간이 중요 한 특정 문제를 합니다.  각 핫픽스 문제, 원인 및 해결을 자세히 설명 하는 해당 Microsoft 기술 자료 문서를 사용 하 여 해제 됩니다.

핫픽스 다운로드 되 고 Azure Stack에 대 한 일반 전체 업데이트 패키지와 마찬가지로 설치 합니다. 그러나 전체 업데이트를 달리 핫픽스는 분 후에 설치할 수 있습니다. Azure Stack 운영자 핫픽스를 설치 하는 경우 유지 관리 기간을 설정 하는 것이 좋습니다. 핫픽스는 핫픽스 적용 된 경우 쉽게 확인할 수 있도록 Azure Stack 클라우드에의 버전을 업데이트 합니다. 지원 되는 Azure Stack의 각 버전에 대 한 별도 핫픽스를 제공 합니다. 특정 반복에 대 한 각 수정 누적 되며 같은 버전에 대 한 이전 업데이트가 포함 되어 있습니다. 자세한 내용은 기술 자료에 해당 하는 수정에 특정 핫픽스의 적용 가능성에 대 한 문서.  

## <a name="keep-your-system-under-support"></a>지원 시스템을 유지 합니다.

지원을 받으려면 계속 하려면 두어야 Azure Stack 배포 현재 합니다. 업데이트 지연 정책을 다음과 같습니다. Azure Stack에 배포 하에서 계속 지원에 대 한 가장 최근에 출시 된 업데이트 버전을 실행 하거나 두 이전 업데이트 버전 중 하나를 실행 해야 합니다. 핫픽스는 주요 업데이트 버전을 고려 하지 않습니다. 경우 Azure Stack 클라우드에 뒤 *두 개 이상의 업데이트*, 준수 것으로 간주 되 고으로 업데이트 해야 적어도 최소 지원된 버전 지원을 받을 수 있습니다.

예를 들어 최신 업데이트 버전이 1805를 이전 두 업데이트 패키지 된 버전 1804 및 1803을 1803와 1804 유지 지원 합니다. 그러나 1802 지원 되지 않습니다. 정책 두 개 또는 한 달에 대 한 릴리스 없음 경우에 마찬가지입니다. 예를 들어, 현재 릴리스는 1805 했는데 1804 릴리스 없음, 1803 및 1802 이전 두 업데이트 패키지 유지 지원 합니다.

Microsoft 소프트웨어 업데이트 패키지 비 누적 되며 이전 업데이트 패키지 또는 핫픽스 필수 구성 요소로 필요 합니다. 하나 이상의 업데이트를 지연 하려는 경우 최신 버전으로 이동 하려는 경우 전체 런타임을 것이 좋습니다.

## <a name="get-support"></a>지원 받기

Azure Stack Azure와 같은 지원 프로세스를 따릅니다. 기업 고객에서 설명한 프로세스를 따르면 [Azure 지원 요청을 만드는 방법](/azure/azure-supportability/how-to-create-azure-support-request)합니다. 고객의 서비스 공급자 (CSP (클라우드) 인 경우 지원에 대 한 CSP가 사용자에 게 문의 합니다.  자세한 내용은 [Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Stack에서 업데이트 관리](azure-stack-updates.md)