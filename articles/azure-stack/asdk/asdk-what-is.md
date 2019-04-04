---
title: Azure Stack 개발 키트 (ASDK)를 소개 | Microsoft Docs
description: 기능에 대해 설명 합니다 ASDK 및 Microsoft Azure Stack을 평가 하기 위한 일반적인 사용 사례입니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 54eb2ff43a5f36999294b8d0c580bc425ab65b28
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629077"
---
# <a name="what-is-the-azure-stack-development-kit"></a>Azure Stack 개발 키트 란?
[Microsoft Azure Stack 통합 시스템](../azure-stack-poc.md) 4-16 노드에서 크기 및 하드웨어 파트너와 Microsoft에서 지원 됩니다. 프로덕션 워크로드용으로 새로운 시나리오를 활용하려면 Azure Stack 통합 시스템을 사용하세요. 통합된 시스템 인프라를 관리 하 고 서비스를 제공 하는 Azure Stack 운영자 인 경우 참조 우리의 [연산자 설명서](https://docs.microsoft.com/azure/azure-stack)합니다.

Azure Stack 개발 키트 (ASDK)는 단일 노드 배포를 다운로드 하 고 사용할 수 있는 Azure Stack **무료로**합니다. 모든 ASDK 구성 요소가 충족 하거나 초과 해야 하는 단일 호스트 서버 컴퓨터에서 실행 되는 가상 컴퓨터에 설치 합니다 [최소 하드웨어 요구 사항을](asdk-deploy-considerations.md#hardware)합니다. Azure Stack을 평가 하 고 최신 응용 프로그램에서 Azure를 사용 하 여 일관 된 도구 및 Api를 사용 하 여 개발할 수 있는 환경을 제공 하는 ASDK 것을 *비프로덕션* 환경입니다. 

> [!IMPORTANT]
> 사용 하거나 프로덕션 환경에서 지원 되는 ASDK은 사용 하는 것이 없습니다.

없기 때문에 단일 개발 키트 호스트 컴퓨터에 배포 된 모든 ASDK 구성 요소, 제한 된 물리적 리소스 사용할 수 있습니다. ASDK 배포를 사용 하 여 Azure Stack 인프라 Vm 및 테 넌 트 Vm에 공존할 같은 서버 컴퓨터입니다. 소수 자릿수 또는 성능 평가용이 구성을 사용 하는 것이 없습니다.

ASDK Azure와 일관 된 하이브리드 클라우드 환경에서 제공 하도록 설계 됩니다.
- **관리자** (Azure Stack 연산자). ASDK가 평가 하 고 사용 가능한 Azure Stack 서비스에 대 한 알아볼 수 있는 훌륭한 리소스입니다.
- **개발자**합니다. 하이브리드 또는 최신 응용 프로그램 온-프레미스 (개발/테스트 환경)를 개발 하는 ASDK은 사용할 수 있습니다. 이 Azure Stack 프로덕션 배포와 함께 또는 그 이전에 개발 환경의 반복성을 제공합니다. 

ASDK에 자세히 알아보려면이 짧은 비디오를 시청 하세요.

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>ASDK 및 다중 노드에 해당 하는 Azure Stack의 차이점
단일 노드 ASDK 배포 다중 노드 Azure Stack 배포에서 고려해 야 하는 몇 가지 중요 한 방식 다릅니다.

|설명|ASDK|Azure Stack 다중 노드|
|-----|-----|-----|
|**규모**|모든 구성 요소는 단일 노드 서버 컴퓨터에 설치 됩니다.|4-16 노드의 크기에 이르기까지 다양 합니다.|
|**복원력**|단일 노드 구성은 고가용성을 제공 하지 않습니다.|[고가용성](../azure-stack-overview.md#providing-high-availability) 기능을 지원 합니다.|
|**네트워킹**|ASDK 호스트에는 모든 ASDK 네트워크 트래픽을 라우팅합니다. 요구 사항은 없습니다 스위치를 추가 합니다.|복잡 [라우팅 인프라 네트워크](../azure-stack-network.md#network-infrastructure) 다중 노드 배포에 반드시-Top-of-rack TOR (), 베이스 보드 관리 컨트롤러 (BMC) 및 테두리 (데이터 센터 네트워크) 스위치를 포함 합니다.|
|**패치 및 업데이트 프로세스**|ASDK의 새 버전으로 이동 하려면 개발 키트 호스트 컴퓨터에서 ASDK 다시 배포 해야 합니다.|[패치 및 업데이트](../azure-stack-updates.md) 설치 된 Azure Stack 버전을 업데이트 하는 데 사용 하는 프로세스입니다.|
|**지원**|MSDN Azure Stack 포럼입니다. Microsoft 고객 서비스 및 지원 (CSS) 지원은 *되지* 비-프로덕션 환경에 사용할 수 있습니다.|[MSDN Azure Stack 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) 전체 CSS 지원 합니다.|
| | |

## <a name="learn-about-available-services"></a>사용 가능한 서비스에 대해 알아보기
Azure Stack 운영자 서비스 가능 사용할 수 있는 사용자에 게 알 필요가 있습니다. Azure Stack Azure 서비스의 하위 집합을 지원 하 고 지원 되는 서비스 목록은 시간이 지남에 따라 개선 될 것입니다.

### <a name="foundational-services"></a>기본 서비스
기본적으로 Azure Stack에는 다음 "기본 서비스"이 포함 됩니다는 ASDK를 배포 하는 경우:
- 컴퓨팅
- Storage
- 네트워킹
- Key Vault

이러한 기본 서비스를 사용 하 여 최소 구성으로 사용자에 게 인프라-as a Service (IaaS)를 제공할 수 있습니다.

### <a name="additional-services"></a>서비스
현재, 다음과 같은 추가 플랫폼-as a Service (PaaS) 서비스가 지원 됩니다.
- App Service
- Azure 기능
- SQL 및 MySQL 데이터베이스

> [!NOTE]
> 이러한 서비스 수 적용 하기 전에 사용할 수 있는 사용자에 게 추가 구성이 필요 하 고는 ASDK를 설치할 때 기본적으로 사용할 수 없는.

## <a name="service-roadmap"></a>서비스 로드맵
Azure Stack 추가 Azure 서비스에 대 한 지원을 추가 계속 됩니다. Azure Stack을 사용 하 여 다음 단계에 대 한 자세한 참조를 [Azure Stack 로드맵](https://azure.microsoft.com/roadmap/?tag=azure-stack)합니다. 


## <a name="next-steps"></a>다음 단계
Azure Stack 평가 시작 하려면 첫 번째 [최신 ASDK 다운로드](asdk-download.md) ASDK 호스트 컴퓨터를 준비 합니다. 개발 키트 호스트를 준비한 후에 ASDK를 설치 하 고 Azure Stack을 사용 하려면 관리자 및 사용자 포털에 로그인 수 있습니다.