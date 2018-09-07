---
title: 관리자 포털을 사용 하 여 Azure Stack에서 | Microsoft Docs
description: Azure Stack 운영자, 관리자 포털을 사용 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.openlocfilehash: cea59d061dadfa3e10330cbce7b6005a286524cc
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024169"
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>관리자 포털을 사용 하 여 Azure Stack에서

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

두 가지 포털에서 Azure Stack; 사용자 포털 및 관리자 포털 (라고도 합니다 *테 넌 트* 포털입니다.) Azure Stack 운영자로 Azure Stack의 작업과 일상적인 관리에 대 한 관리자 포털을 사용할 수 있습니다.

## <a name="access-the-administrator-portal"></a>관리자 포털에 액세스

개발 키트 환경에 대 한 있습니다 먼저 확인 해야 할 수 있습니다 [개발 키트 호스트에 연결할](azure-stack-connect-azure-stack.md) 원격 데스크톱 연결을 통해 또는 가상 사설망 (VPN)을 통해.

관리자 포털에 액세스 하려면 포털 URL 및 Azure Stack 운영자의 자격 증명을 사용 하 여 로그인을 검색 합니다. 통합된 시스템에 대 한 URL 다릅니다 포털 영역 이름 및 Azure Stack 배포의 외부 정규화 된 도메인 이름 (FQDN)에 기반 합니다.

| Environment | 관리자 포털 URL |   
| -- | -- | 
| 개발 키트| https://adminportal.local.azurestack.external  |
| 통합 시스템 | https://adminportal.&lt; *지역*&gt;.&lt; *FQDN*&gt; | 
| | |

 ![관리자 포털](media/azure-stack-manage-portals/admin-portal.png)

관리자 포털에서와 같은 작업을 수행할 수 있습니다.

* (시스템 상태, 업데이트, 용량 등을 포함 합니다.) 인프라를 관리 합니다.
* 마켓플레이스 채우기
* 사용자에 대 한 구독 만들기
* 계획 및 제품은 만들기

합니다 **빠른 시작 자습서** 타일은 가장 일반적인 작업에 대 한 온라인 설명서에 대 한 링크를 제공 합니다.

운영자에 있지만 리소스를 만들 수 있습니다 virtual machines, virtual network 관리자 포털에서 저장소 계정 등을 수행 해야 [사용자 포털에 로그인](user/azure-stack-use-portal.md) 을 만들고 리소스를 테스트 합니다.

>[!NOTE]
>합니다 **가상 머신 만들기** 빠른 시작 자습서 타일에는 링크에는 관리자 포털에서 가상 컴퓨터를 만들 수 있지만이 처음으로 배포 된 후 Azure Stack의 유효성을 검사 하기 위한 것만입니다.

## <a name="understand-subscription-behavior"></a>구독 동작 이해

하나의 구독 관리자 포털을 사용할 수 있습니다. 이 구독은 합니다 *기본 공급자 구독*합니다. 다른 구독을 추가할 수 없으며 관리자 포털에서 사용할 수 없습니다.

Azure Stack 운영자로 관리자 포털에서 사용자 (자신 포함)에 대 한 구독을 추가할 수 있습니다. 사용자 (자신 포함)에서 액세스 하 고 이러한 구독을 사용 하 여 수를 **사용자** 포털입니다. 그러나 사용자 포털 관리자 포털의 관리 또는 운영 기능 중 하나에 대 한 액세스를 제공 하지 않습니다.

관리자 및 사용자 포털은 Azure Resource Manager의 개별 인스턴스에 의해 지원 됩니다. 이 Resource Manager 구분으로 인해 구독 포털을 초과 하지 않습니다. 예를 들어, Azure Stack 운영자에 로그인 하면 사용자 포털에 액세스할 수 없습니다는 *기본 공급자 구독*합니다. 없지만 모든 관리 기능에 대 한 액세스를 사용할 수 있는 공용 서비스에서 직접 구독을 만들 수 있습니다. 사용자 포털에 로그인으로 테 넌 트 사용자를 라고 합니다.

  >[!NOTE]
  >개발 키트 환경에서 Azure Stack 연산자와 같은 테 넌 트 디렉터리에 사용자가 속할 경우 차단 되지는 않지만 관리자 포털에 로그인 합니다. 그러나 이러한는 관리 기능 중 하나를 액세스할 수 없습니다. 또한 관리자 포털에서 구독 또는 액세스 제공에 사용자 포털에서 사용할 수 있는 추가할 수는 없습니다.

## <a name="administrator-portal-tips"></a>관리자 포털 팁

### <a name="customize-the-dashboard"></a>대시보드를 사용자 지정

대시보드는 기본 타일 집합이 포함 되어 있습니다. 선택할 수 있습니다 **대시보드 편집** 기본 대시보드를 수정 하거나 선택할 **새 대시보드** 사용자 지정 대시보드를 추가 합니다. 대시보드에 타일을 쉽게 추가할 수 있습니다. 예를 들어, 선택할 수 있습니다 **새로 만들기**를 마우스 오른쪽 단추로 클릭 **제공 + 계획**를 선택한 후 **대시보드에 고정**합니다.

### <a name="quick-access-to-online-documentation"></a>온라인 설명서에 대 한 빠른 액세스

Azure Stack 연산자 설명서에 액세스 하려면 도움말을 사용 하 고 관리자 포털의 오른쪽 위 모퉁이에서 아이콘 (물음표)를 지원 합니다. 아이콘, 커서를 이동 하 고 선택한 **도움말 + 지원**합니다.

### <a name="quick-access-to-help-and-support"></a>도움말 및 지원에 대 한 빠른 액세스

관리자 포털의 오른쪽 위 모서리에 있는 도움말 및 지원 (물음표) 아이콘을 선택 하 고 선택한 **새 지원 요청**, 다음 결과 중 하나가 발생 합니다.

- 통합된 시스템을 사용 하는 경우이 작업에 열 수 있는 직접 지원 티켓을 사용 하 여 Microsoft 고객 지원 서비스 (CSS) 사이트를 엽니다. 가리킵니다 [지원을 받을 수 있는 위치](azure-stack-manage-basics.md#where-to-get-support) Microsoft 지원을 통해 또는 프로그램 원래 장비 제조업체 (OEM) 하드웨어 공급 업체 지원을 통해 이동 해야 하면 알아야 합니다.
- 개발 키트를 사용 하는 경우이 작업 직접 Azure Stack 포럼 사이트를 엽니다. 이러한 포럼을 정기적으로 모니터링 합니다. 개발 키트는 평가 환경, 이므로 Microsoft CSS를 통해 제공 되는 공식 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack의 지역 관리](azure-stack-region-management.md)
