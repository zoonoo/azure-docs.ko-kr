---
title: Azure 분석 서비스를 위한 온-프레미스 데이터 게이트웨이 설치 | 마이크로 소프트 문서
description: Azure Analysis Services 서버에서 온-프레미스 데이터 원본에 연결하도록 온-프레미스 데이터 게이트웨이를 설치하고 구성하는 방법을 알아봅니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f6218b32fb9574adf62384d2a6ee5a62f3788de8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062152"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>온-프레미스 데이터 게이트웨이 설치 및 구성

동일한 지역에 하나 이상의 Azure Analysis Services 서버를 온-프레미스 데이터 원본에 연결하는 경우 온-프레미스 데이터 게이트웨이가 필요합니다.  설치하는 게이트웨이는 Power BI, Power Apps 및 논리 앱과 같은 다른 서비스에서 사용하는 게이트웨이와 동일하지만 Azure Analysis Services에 설치할 때 완료해야 하는 몇 가지 추가 단계가 있습니다. 이 설치 문서는 **Azure 분석 서비스에만**해당됩니다. 

Azure 분석 서비스가 게이트웨이에서 작동하는 방식에 대해 자세히 알아보려면 [온-프레미스 데이터 원본에 연결 참조하세요.](analysis-services-gateway.md) 고급 설치 시나리오 및 일반적으로 게이트웨이에 대한 자세한 내용은 [온-프레미스 데이터 게이트웨이 설명서를](/data-integration/gateway/service-gateway-onprem)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

**최소 요구 사항:**

* .NET 4.5 Framework
* 윈도우 8 / 윈도우 서버 2012 R2의 64 비트 버전 (이상)

**권장:**

* 8 코어 CPU
* 8GB 메모리
* 윈도우 8 / 윈도우 서버 2012 R2의 64 비트 버전 (이상)

**중요한 고려 사항:**

* 설치하는 동안 Azure로 게이트웨이를 등록하는 경우 구독에 대한 기본 지역이 선택됩니다. 다른 구독 및 지역을 선택할 수 있습니다. 서버가 여러 지역에 있는 경우 각 지역에 대한 게이트웨이를 설치해야 합니다. 
* 도메인 컨트롤러에 게이트웨이를 설치할 수 없습니다.
* 단일 컴퓨터에는 하나의 게이트웨이를 설치할 수 있습니다.
* 유지되고 대기 상태로 전환되지 않는 컴퓨터에 게이트웨이를 설치합니다.
* 네트워크에 무선으로만 연결되는 컴퓨터에 게이트웨이를 설치하지 마십시오. 성능이 감소될 수 있습니다.
* 게이트웨이를 설치할 때 컴퓨터에 로그인한 사용자 계정에는 서비스로 로그온 권한이 있어야 합니다. 설치가 완료되면 온-프레미스 데이터 게이트웨이 서비스가 NT SERVICE\PBIEgwService 계정을 사용하여 서비스로 로그온합니다. 다른 계정은 설정하는 동안 지정하거나 설정 완료 후 서비스에서 지정할 수 있습니다. 그룹 정책 설정에서 설치할 때 로그인한 계정 및 사용자가 선택한 서비스로 로그온 권한이 있는 서비스 계정을 둘 다 허용하는지 확인합니다.
* 게이트웨이를 등록한 구독과 동일한 [테넌트](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant)의 Azure AD 계정으로 Azure에 로그인합니다. 게이트웨이를 설치 및 등록할 때 Azure B2B(게스트) 계정은 지원되지 않습니다.
* 데이터 원본이 Azure VNet(Virtual Network)에 있는 경우 [AlwaysUseGateway](analysis-services-vnet-gateway.md) 서버 속성을 구성해야 합니다.

## <a name="download"></a><a name="download"></a>다운로드

 [게이트웨이 다운로드](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a><a name="install"></a>설치

1. 설치를 실행합니다.

2. **온-프레미스 데이터 게이트웨이를**선택합니다.

   ![선택](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. 위치를 선택하고 조건에 동의한 다음 **설치**를 클릭합니다.

   ![설치 위치 및 사용 조건](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Azure에 로그인합니다. 계정은 테넌트의 Azure Active Directory에 있어야 합니다. 게이트웨이 관리자가 이 계정을 사용합니다. 게이트웨이를 설치 및 등록할 때 Azure B2B(게스트) 계정은 지원되지 않습니다.

   ![Azure에 로그인](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > 도메인 계정으로 로그인하는 경우 Azure AD의 조직 계정에 매핑됩니다. 조직 계정은 게이트웨이 관리자로 사용됩니다.

## <a name="register"></a><a name="register"></a>등록

Azure에서 게이트웨이 리소스를 만들기 위해 게이트웨이 클라우드 서비스와 함께 설치한 로컬 인스턴스를 등록해야 합니다. 

1.  **이 컴퓨터에 새 게이트웨이 등록**을 선택합니다.

    ![등록](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. 게이트웨이에 대한 이름 및 복구 키를 입력합니다. 기본적으로 게이트웨이는 구독의 기본 지역을 사용합니다. 다른 지역을 선택해야 하는 경우 **지역 변경**을 선택합니다.

    > [!IMPORTANT]
    > 복구 키를 안전한 장소에 저장합니다. 복구 키는 게이트웨이 인수, 마이그레이션 또는 복원에 필요합니다. 

   ![등록](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a><a name="create-resource"></a>Azure 게이트웨이 리소스 만들기

게이트웨이를 설치하고 등록한 후에는 Azure에서 게이트웨이 리소스를 만들어야 합니다. 게이트웨이를 등록할 때 사용했던 동일한 계정으로 Azure에 로그인합니다.

1. Azure 포털에서 **리소스 만들기를**클릭한 다음 **온-프레미스 데이터 게이트웨이를**검색한 다음 **을 클릭합니다.**

   ![게이트웨이 리소스 만들기](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. **연결 게이트웨이 만들기**에서 이러한 설정을 입력합니다.

   * **이름**: 게이트웨이 리소스의 이름을 입력합니다. 

   * **구독**: 게이트웨이 리소스와 연결할 Azure 구독을 선택합니다. 
   
     기본 구독은 로그인하는 데 사용한 Azure 계정을 기반으로 합니다.

   * **리소스 그룹**: 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다.

   * **위치**: 게이트웨이를 등록한 지역을 선택합니다.

   * **설치 이름**: 게이트웨이 설치가 아직 선택되지 않은 경우 컴퓨터에 설치하고 등록한 게이트웨이를 선택합니다. 

     완료하면 **만들기**를 클릭합니다.

## <a name="connect-servers-to-the-gateway-resource"></a><a name="connect-servers"></a>게이트웨이 리소스에 서버 연결

1. Azure Analysis Services 서버 개요에서 **온-프레미스 데이터 게이트웨이**를 클릭합니다.

   ![게이트웨이에 서버 연결](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. **연결할 온-프레미스 데이터 게이트웨이 선택**에서 게이트웨이 리소스를 선택한 다음 **선택한 게이트웨이 연결**을 클릭합니다.

   ![게이트웨이 리소스에 서버 연결](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > 게이트웨이가 목록에 나타나지 않으면 서버는 게이트웨이를 등록할 때 지정한 지역과 동일한 지역에 있지 않을 가능성이 있습니다.

    서버와 게이트웨이 리소스 간의 연결이 성공하면 상태가 **연결됨**으로 표시됩니다.


    ![서버를 게이트웨이 리소스 성공에 연결](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

이것으로 끝입니다. 포트를 열거나 문제 해결을 수행해야 하는 경우 [온-프레미스 데이터 게이트웨이](analysis-services-gateway.md)를 확인하세요.

## <a name="next-steps"></a>다음 단계

* [Analysis Services 관리](analysis-services-manage.md)   
* [Azure Analysis Services에서 데이터 가져오기](analysis-services-connect.md)   
* [Azure Virtual Network에서 데이터 원본에 게이트웨이 사용](analysis-services-vnet-gateway.md)
