---
title: "Azure RemoteApp에 대한 청구 변경 | Microsoft Docs"
description: "Azure RemoteApp에 대한 비용 청구를 중지하는 방법을 알아봅니다."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 8f94da9a-7848-4ddc-b7b7-d9c280ccf4d7
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mbaldwin
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 32fc673eeef01e80c73375bf264206beea8cfbe5
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="migrate-from-azure-remoteapp-to-mycloudit"></a>Azure RemoteApp에서 MyCloudIT로 마이그레이션 

**현재 Microsoft Azure RemoteApp을 사용하나요?**: MyCloudIT는 Microsoft Azure에서 계속 실행하면서 ARA(Azure RemoteApp) 컬렉션을 MyCloudIT 관리 플랫폼으로 마이그레이션하는 자동화 도구를 제작했습니다.

**Azure Resource Manager 활용**: MyCloudIT 플랫폼으로 마이그레이션을 완료하면 Azure의 새로운 Azure Resource Manager포털에 즉시 액세스할 수 있습니다. 이 포털에는 배포가 비즈니스 필요를 지원하도록 Virtual Machine 크기에 액세스를 비롯하여 Microsoft Azure에서 제공하는 모든 새로운 기능과 혁신이 포함됩니다.

**사용자의 요구에 적합한 솔루션을 확인하는 동시에 테스트**: MyCloudIT 마이그레이션 도구는 현재 ARA 사용자가 ARA를 계속 사용하면서 마이그레이션 프로세스를 시작하고 동시에 테스트할 수 있도록 제작되었습니다.  사용자는 마이그레이션 및 테스트가 완료될 때까지 ARA에서 유지됩니다.  마이그레이션 도구는 일반적인 ARA 컬렉션을 처리하도록 제작되었습니다.  특이하고 비표준적인 시나리오에 해당한다고 생각되는 경우 [sales@conexlink.com](mailto:sales@conexlink.com)으로 연락주시면 마이그레이션에 도움이 되는 맞춤형 계획을 제공해드릴 수 있습니다.

**DaaS(Desktop-as-a-Service) 기능**: MyCloudIT는 익숙한 RemoteApp 기능뿐만 아니라 최소한의 사용자 요구도 없이 동일한 월별 비용으로 전체 DaaS 기능도 제공합니다.

## <a name="what-we-will-do-for-you"></a>사용자를 위한 기능

MyCloudIT는 자동화된 마이그레이션 도구를 사용하여 사용자 구독의 Azure 클래식 포털에서 Azure Resource Manager 포털로 Azure RemoteApp 템플릿의 마이그레이션을 자동화합니다.  

> [!NOTE]
> Azure RemoteApp 템플릿은 원래 Azure RemoteApp 배포와 동일한 Azure 지역에 있어야 합니다.  마이그레이션하는 동안 Azure 지역 또는 Azure 구독을 변경해야 하는 경우 [sales@conexlink.com](mailto:sales@conexlink.com)에서 추가 지침을 문의하세요.

MyCloudIT 마이그레이션 도구를 사용한 자동화된 마이그레이션 프로세스에 대한 자세한 내용은 아래를 읽어보세요.

1. 마이그레이션 도구는 기존의 모든 ARA 배포에 대해 현재 구독을 검색합니다.  
2. 마이그레이션할 ARA 컬렉션을 한 번에 하나씩 선택합니다.  컬렉션이 여러 개 있는 경우 도구를 여러 번 실행합니다.
3. 레거시 데이터를 검색하거나 UPD를 새 배포에 수동으로 매핑할 수 있도록 UPD(사용자 프로필 디스크)를 새 배포에 복사하는 옵션이 있습니다. UPD를 복사하도록 선택하는 경우 UPD를 저장하고 UPD 이름을 사용자의 각 이름에 매핑하는 텍스트 파일을 포함합니다.  UPD가 RDSMGMT 서버 `F:\Shares\LegacyUPD`의 공유에 복사되고 공유 `\\RDSmgmt\LegacyUPD`를 통해 공개됩니다. 
4. 마이그레이션에는 현재 ARA 배포에 대해 가동 중지 시간이 필요하지 않습니다.  그러나 복사 후 UPD(ARA에서)에 변경 내용이 있는 경우 해당 변경 내용은 Azure Resource Manager 포털에 저장된 UPD에 반영되지 않습니다. 
5. 클래식 Azure Virtual Network에 도메인 컨트롤러 및 파일 서버와 같은 추가 VM이 있는 경우 새로운 Azure Resource Manager 포털에서 기존 클래식 Azure Virtual Network와 생성된 새로운 Virtual Network 간에 VNet 피어링을 설정합니다.
6. 자동화된 솔루션은 기존 ARA 배포가 하이브리드 배포인 경우 기존 Azure Virtual Network와 새 Virtual Network 간의 VNet 피어링만 설정하므로 기존의 클래식 Virtual Network에서 Windows Server Active Directory 도메인 컨트롤러로 인증합니다. 컬렉션에 대해 VNet 피어링을 설정하지 않았지만 VNet 피어링이 필요한 경우 [sales@conexlink.com](mailto:sales@conexlink.com)으로 문의해 주시면 추가 비용 없이 VNet 피어링을 기꺼이 구성해 드립니다.
7. 자동화된 솔루션은 Azure DNS 구성이 새 Virtual Network 설정으로 업데이트되도록 하여 새로운 배포가 클래식 VNet의 기존 도메인 컨트롤러에 연결될 수 있도록 합니다.
8. 자동화된 솔루션은 새로운 Virtual Network 생성하고 기존 Windows Server Active Directory Server가 있는 배포에 대해 VNet 피어링을 설정하는 동안 IP 주소 충돌이 없는지 확인합니다.
9. 인증에 Azure AD만 사용하는 경우 MyCloudIT는 새로운 Windows Server Active Directory 도메인을 만들고 Azure AD Connect를 사용하여 기존 Azure AD 인스턴스와 MyCloudIT에서 만든 새 Windows Server Active Directory 도메인 간에 사용자를 동기화합니다.
10. Windows Server Active Directory 도메인을 사용하여 ARA 사용자를 인증하는 경우 자동화된 솔루션은 VNet 피어링을 통해 새로운 MyCloudIT 배포를 기존 Windows Server Active Directory 도메인 컨트롤러에 연결합니다.
11. 인증에 Azure Active Directory Domain Services를 사용하는 경우 마이그레이션이 가능하지만 문의하여 사용자에게 맞는 사용자 지정 마이그레이션 계획을 작성하는 것이 좋습니다.  [sales@conexlink.com](mailto:sales@conexlink.com)으로 전자 메일을 보내주세요. 
12. 마이그레이션할 컬렉션을 확인한 후에는 자동화된 솔루션이 ARA 컬렉션 및 사용자 프로필 디스크(선택 사항)를 새로운 MyCloudIT 기반 원격 앱 솔루션으로 마이그레이션하는 동안 앉아서 쉬면 됩니다.
13. 배포가 완료되면 ARA에 게시된 것과 동일한 응용 프로그램을 다시 게시하고 배포 후 응용 프로그램을 더 게시할 수 있게 됩니다.

## <a name="post-migration-benefits"></a>마이그레이션 후 이점

1. 원격 앱 배포의 전체 수명 주기를 관리할 수 있는 관리 콘솔을 제공합니다.
2. 포털에서 Virtual Machines를 관리할 수 있게 됩니다.  필요에 따라 개별 VM을 시작/중지 및 크기 조정합니다.
3. 관리 콘솔은 관리 포털에서 사용자/그룹을 만들고 관리하는 기능을 제공합니다.
4. 관리 콘솔은 Office 365와 사용자를 동기화하여 동일한 로그온 환경을 만드는 기능을 제공합니다.
5. 관리 콘솔은 사용자의 이중 비용이나 최소한의 사용자 요구도 없이 추가 원격 앱 및 데스크톱 컬렉션을 만드는 기능을 제공합니다. 
6. 관리 콘솔은 새 원격 앱 응용 프로그램을 게시하는 기능을 제공합니다.
7. 관리 콘솔은 특정 시간 동안에만 솔루션이 필요한 경우 원격 앱 배포의 시작 및 종료를 예약하는 기능을 제공합니다.
8. 관리 콘솔은 고객 데이터의 문서 보존 기록을 제공하는 Azure Backup 에이전트의 설치 및 구성을 자동화하는 기능을 제공합니다.
9. 관리 콘솔은 배포의 성능 메트릭에 대한 액세스를 제공합니다.  따라서 성능 관리 도구를 추가로 설치하지 않고도 잠재적인 성능 병목 현상을 확인할 수 있는 기능이 제공됩니다.
10. 세션 호스트가 여러 개 있는 경우 자동 크기 조정을 통해 필요한 세션 호스트만 실행되도록 할 수 있습니다.
11. MyCloudIT는 MyCloudIT 도메인 이름을 통해 RDWeb 게이트웨이 서버에 대한 액세스를 제공합니다.  또한 URL을 최종 사용자 브랜딩을 위한 사용자 지정 URL로 다시 매핑하는 기능도 제공합니다.

## <a name="prerequisites-for-migration"></a>마이그레이션을 위한 필수 조건

1. 현재 Azure RemoteApp 솔루션을 호스트하는 Azure 구독에 대한 액세스 권한이 있어야 합니다.
2. 구독 내에서 템플릿을 마이그레이션하고 새 MyCloudIT 배포를 만들고 수정하기 위한 포털 사용 권한을 부여해야 합니다.
3. Azure Remote App의 제한 사항으로 인해, 한 컬렉션당 3번만 마이그레이션할 수 있습니다.  컬렉션을 4번 이상 마이그레이션해야 하는 경우 Azure에 티켓을 제기하여 내보내기 수를 늘리거나, 문의하여 내보내기 수를 늘리기 위한 ARA 요청에 대해 도움을 받을 수 있습니다.

## <a name="mycloudit-billing"></a>MyCloudIT 요금 청구

Azure 전체 비용을 예측 및 관리하는 방법은 [RemoteApp 솔루션에 대한 MyCloudIT 가격(PDF)](https://mcitdocuments.blob.core.windows.net/terms/MyCloudIT_Pricing_Overview.pdf)을 참조하세요.

여전히 질문이 있는 경우 [sales@conexlink.com](mailto:sales@conexlink.com)으로 문의하거나 전체 데모 비디오 [Azure RemoteApp 마이그레이션 도구 - MyCloudIT](https://www.youtube.com/watch?v=YQ_1F-JeeLM&t=482s)를 시청하세요. 


