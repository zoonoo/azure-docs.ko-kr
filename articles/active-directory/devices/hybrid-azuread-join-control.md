---
title: 하이브리드 Azure Active Directory 가입 디바이스 구성 방법 | Microsoft Docs
description: 하이브리드 Azure Active Directory 가입 디바이스를 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: d49b5404f1a2b4ac7fa4cc170ccc010a28bf98a2
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42143747"
---
# <a name="how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>디바이스의 하이브리드 Azure AD 조인을 제어하는 방법

하이브리드 Azure AD 조인은 Azure AD를 사용하여 온-프레미스 도메인 가입 디바이스를 자동으로 등록하는 프로세스입니다. 모든 디바이스가 자동으로 등록되지 않도록 하려는 경우가 있습니다. 예를 들어, 초기 롤아웃 동안 모든 항목이 예상대로 작동하는지 확인하려는 경우가 여기에 해당합니다.

이 문서에서는 디바이스의 하이브리드 Azure AD 조인을 제어하는 방법에 대한 지침을 제공합니다. 


## <a name="prerequisites"></a>필수 조건

이 문서에서는 사용자가 다음에 대해 잘 알고 있다고 가정합니다.

-  [Azure Active Directory의 장치 관리 소개](../device-management-introduction.md)
 
-  [하이브리드 Azure Active Directory 조인 구현을 계획하는 방법](hybrid-azuread-join-plan.md)

-  [관리되는 도메인](hybrid-azuread-join-managed-domains.md) 또는 [페더레이션 도메인](hybrid-azuread-join-federated-domains.md)에 대한 하이브리드 Azure Active Directory 조인 구성



## <a name="control-windows-current-devices"></a>Windows 최신 디바이스 제어

Windows 데스크톱 운영 체제를 실행하는 디바이스의 경우 지원되는 버전은 Windows 10주년 업데이트(버전 1607) 이상입니다. 가장 좋은 방법은 최신 버전의 Windows 10으로 업그레이드하는 것입니다.

모든 Windows 최신 디바이스는 디바이스 시작 시 또는 사용자 로그인 시 자동으로 Azure AD에 등록됩니다. GPO(그룹 정책 개체) 또는 System Center Configuration Manager를 사용하여 이 동작을 제어할 수 있습니다.

Windows 최신 디바이스를 제어하려면 다음을 수행해야 합니다. 

1.  **모든 장치**: 자동 장치 등록을 사용하지 않도록 설정합니다.
2.  **선택한 장치**: 자동 장치 등록을 사용하도록 설정합니다.

모든 항목이 예상대로 작동하는지 확인한 후에는 모든 디바이스에 대한 자동 디바이스 등록을 다시 사용하도록 설정할 수 있습니다.



## <a name="group-policy-object"></a>그룹 정책 개체 

GPO **도메인에 가입된 컴퓨터를 디바이스로 등록**을 배포하여 디바이스의 디바이스 등록 동작을 제어할 수 있습니다.

**GPO를 설정하려면**

1.  **서버 관리자**를 연 다음 **도구\> 그룹 정책 관리**로 이동합니다.

2.  자동 등록을 사용하지 않거나 사용하도록 설정하려는 도메인에 해당하는 도메인 노드로 이동합니다.

3.  **그룹 정책 개체**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 선택합니다.

4.  그룹 정책 개체의 이름(예: *하이브리드 Azure AD 조인*)을 입력합니다. 

5.  **확인**을 클릭합니다.

6.  새 GPO를 마우스 오른쪽 단추로 클릭하고 **편집**을 선택택합니다.

7.  **컴퓨터 구성\> 정책 \> 관리 템플릿 \> Windows 구성 요소 \> 장치 등록**으로 이동합니다. 

8.  마우스 오른쪽 단추로 **도메인 가입 컴퓨터를 디바이스로 등록**을 클릭한 다음, **편집**을 선택합니다.

    > [!NOTE] 
    > 이 그룹 정책 템플릿 이름은 이전 버전의 그룹 정책 관리 콘솔에서 변경되었습니다. 이전 버전의 콘솔을 사용하는 경우 **컴퓨터 구성 \> 정책s \> 관리 템플릿 \> Windows 구성 요소 \> 작업 공간 연결 \> 클라이언트 컴퓨터와 작업 공간 자동 연결**로 이동합니다. 

9.  다음 설정 중 하나를 선택하고 **적용**을 클릭합니다.

    - **사용 안 함** - 자동 장치 등록 방지
    - **사용** - 자동 장치 등록 사용

10. **확인**을 클릭합니다.

사용자가 선택한 위치에 GPO를 연결해야 합니다. 예를 들어, 조직에서 모든 도메인 가입 최신 디바이스에 대해 이 정책을 설정하려면 도메인에 GPO을 연결합니다. 제어되는 배포를 수행하려면 이 정책을 조직 구성 단위 또는 보안 그룹에 속하는 도메인 가입 Windows 최신 디바이스로 설정합니다.

### <a name="configuration-manager-controlled-deployment"></a>Configuration Manager 제어 배포 

클라이언트 설정 **Azure Active Directory에 새 Windows 10 도메인에 연결된 디바이스를 자동으로 등록**을 구성하여 현재 디바이스의 디바이스 등록 동작을 제어할 수 있습니다.


**클라이언트 설정을 지정하려면**

1.  **Configuration Manager**를 열고 **Cloud Services**로 이동동합니다.

2.  **장치 설정** 아래에서 **Azure Active Directory에 새 Windows 10 도메인에 연결된 장치를 자동으로 등록**에 대해 다음 설정 중 하나를 선택합니다.

    - **아니요** - 자동 장치 등록 방지
    - **예** - 자동 장치 등록 사용


3.  **확인**을 클릭합니다.
    

사용자가 선택한 위치에 이 클라이언트 설정을 연결해야 합니다. 예를 들어, 조직의 모든 Windows 최신 디바이스에 대해 이 클라이언트 설정을 구성하려면 클라이언트 설정을 해당 도메인에 연결합니다. 제어되는 배포를 수행하려면 클라이언트 설정을 조직 구성 단위 또는 보안 그룹에 속하는 도메인 가입 Windows 최신 디바이스로 구성할 수 있습니다.

> [!Important]
> 위의 구성은 기존의 도메인 가입 Windows 10 디바이스를 처리하지만, 새로운 도메인 가입 Windows 10 디바이스의 그룹 정책 실제 애플리케이션 또는 Configuration Manager 설정의 잠재적 지연 때문에 새로운 도메인 가입 디바이스가 계속해서 하이브리드 Azure AD 조인을 완료하려고 시도할 가능성이 있습니다. 이를 방지하려면 이전에 하이브리드 Azure AD이 조인된 적이 없고 위의 그룹 정책 설정 또는 Configuration Manager 클라이언트 설정이 이미 적용된 디바이스에서 새 sysprep 이미지(프로비전 방법의 예제로 사용된)를 만드는 것이 좋습니다. 또한 조직의 도메인을 조인하는 새 컴퓨터를 프로비전하는 데 새 이미지를 사용해야 합니다. 

## <a name="control-windows-down-level-devices"></a>Windows 하위 수준 디바이스 제어

Windows 하위 수준 디바이스를 등록하려면 [비-Windows 10 컴퓨터의 Microsoft 작업 공간 연결](https://www.microsoft.com/download/details.aspx?id=53554) 페이지의 다운로드 센터에서 Windows Installer 패키지(.msi)를 다운로드하여 설치해야 합니다.

System Center Configuration Manager 같은 소프트웨어 배포 시스템을 사용하여 패키지를 배포할 수 있습니다. 이 패키지는 quiet 매개 변수를 사용하여 표준 자동 설치 옵션을 지원합니다. [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager) Current Branch는 완료된 등록을 추적하는 기능과 같은 이전 버전의 이점이 추가로 제공됩니다.

설치 관리자에서는 사용자 컨텍스트에서 실행되도록 예약된 작업을 시스템에 만듭니다. 사용자가 Windows에 로그인할 때 이 작업이 트리거됩니다. 이 작업은 Azure AD에서 인증을 받은 후 사용자 자격 증명으로 Azure AD에 디바이스를 자동으로 가입합니다.


디바이스 등록을 제어하려면 선택된 Windows 하위 수준 디바이스 그룹에만 Windows Installer 패키지를 배포해야 합니다. 모든 항목이 예상대로 작동하는지 확인했으면 모든 하위 수준 디바이스에 패키지를 롤아웃할 준비가 된 것입니다.


## <a name="next-steps"></a>다음 단계

* [Azure Active Directory의 장치 관리 소개](../device-management-introduction.md)



