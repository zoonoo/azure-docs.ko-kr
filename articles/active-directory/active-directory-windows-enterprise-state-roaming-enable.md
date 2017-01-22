
---
title: "Azure Active Directory에서 엔터프라이즈 상태 로밍 활성화 | Microsoft Docs"
description: "Windows 장치의 엔터프라이즈 상태 로밍 설정에 대해 자주 묻는 질문과 대답입니다. 엔터프라이즈 상태 로밍은 사용자의 모든 Windows 장치에 통합된 환경을 제공하고 새 장치 구성에 드는 시간을 줄여 줍니다."
services: active-directory
keywords: "엔터프라이즈 상태 로밍, windows 클라우드, 엔터프라이즈 상태 로밍을 활성화하는 방법"
documentationcenter: 
author: femila
manager: swadhwa
editor: curtand
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: 0628dafc9cc4cb05e678f741d913e00116013459
ms.openlocfilehash: 435f801b254a6c423a6ec3e96037ade62e14af29


---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 상태 로밍 활성화
엔터프라이즈 상태 로밍은 프리미엄 Azure Active Directory(Azure AD)를 구독하는 모든 조직에 제공됩니다. Azure AD 구독 방법에 대한 자세한 내용은 [Azure AD 제품 페이지](https://azure.microsoft.com/services/active-directory)를 참조하세요.

엔터프라이즈 상태 로밍을 사용하도록 설정하면 조직에 Azure Rights Management에 대한 제한된 무료 구독의 라이선스가 자동으로 부여됩니다. 이 무료 구독은 엔터프라이즈 상태 로밍 서비스로 동기화된 엔터프라이즈 설정 및 응용 프로그램 데이터를 암호화하고 암호를 해독하도록 제한됩니다. Azure Rights Management의 모든 기능을 사용하려면 유료 구독이 있어야 합니다.

Premium Azure AD 구독을 얻은 후에 다음 단계에 따라 엔터프라이즈 상태 로밍을 사용하도록 설정합니다.

1. Azure 클래식 포털에 로그인합니다.
2. 왼쪽에서 **ACTIVE DIRECTORY**를 선택한 다음 엔터프라이즈 상태 로밍을 활성화할 디렉터리를 선택 합니다.
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)
3. 맨 위에 있는 **구성** 탭으로 이동합니다.
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4. 페이지를 아래로 스크롤하여 **설정 및 엔터프라이즈 앱 데이터 동기화가 허용되는 사용자**를 클릭한 다음 **저장**을 클릭합니다.
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

Windows 10 장치의 경우 엔터프라이즈 상태 로밍 서비스를 사용하여 설정을 로밍하려면 Azure AD ID를 사용하여 장치를 인증해야 합니다. Azure AD에 조인된 장치의 경우 Azure AD ID가 사용자의 기본 로그인이므로 추가 구성이 필요 없습니다. 기존의 온-프레미스 Active Directory를 사용하는 장치의 경우 IT 관리자가 [도메인에 조인된 장치를 Windows 10용 Azure AD 환경에 연결](active-directory-azureadjoin-devices-group-policy.md)해야 합니다.

## <a name="sync-data-storage"></a>데이터 저장소 동기화
엔터프라이즈 상태 로밍 데이터는 Azure Active Directory 인스턴스에 설정된 국가/지역 값에 가장 적합한 하나 이상의 [Azure 지역](https://azure.microsoft.com/regions/)에 호스트됩니다. 엔터프라이즈 상태 로밍 데이터는 북미, EMEA, APAC의 주요 지리적 지역 3개를 기준으로 분할됩니다. 테넌트에 대한 엔터프라이즈 상태 로밍 데이터는 지리적 영역에 로컬로 위치하며 여러 지역에 걸쳐 복제되지 않습니다.  예를 들어 해당 국가/지역 값이 EMEA 국가 중 하나(예: "프랑스" 또는 "잠비아")로 설정된 고객은 해당 데이터가 유럽 내의 Azure 지역 중 하나 이상에서 호스트됩니다.  해당 국가/지역 값이 북미 국가 중 하나(예: "미국" 또는 "캐나다")로 설정된 고객은 해당 데이터가 미국 내의 Azure 지역 중 하나 이상에서 호스트됩니다.  해당 국가/지역 값이 APAC 국가 중 하나(예: "호주" 또는 "뉴질랜드")로 설정된 고객은 해당 데이터가 아시아 내의 Azure 지역 중 하나 이상에서 호스트됩니다.  남미 국가 및 남극 대륙 데이터는 미국 내의 Azure 지역 중 하나 이상에서 호스트됩니다.  국가/지역 값은 Azure AD 디렉터리 생성 프로세스의 일부로 설정되며 나중에 수정할 수 없습니다. 

데이터 저장소 위치에 대한 자세한 내용을 보려면 [Azure 지원](https://azure.microsoft.com/support/options/)에서 티켓을 발행하세요.

## <a name="manage-enterprise-state-roaming"></a>엔터프라이즈 상태 로밍 관리
Azure AD 전역 관리자는 Azure 클래식 포털에서 엔터프라이즈 상태 로밍을 활성화 및 비활성화할 수 있습니다.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

전역 관리자는 특정 보안 그룹으로 설정 동기화를 제한할 수 있습니다.

또한 전역 관리자는 Active Directory 인스턴스 **사용자** 목록에서 특정 사용자를 선택하고 **장치** 탭을 클릭한 다음 설정 및 엔터프라이즈 앱 데이터를 동기화하는 **장치 보기를 선택하여 사용자별 장치 동기화 상태 보고서**를 볼 수도 있습니다.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-device-sync-settings.png)

## <a name="data-retention"></a>데이터 보존
엔터프라이즈 상태 로밍을 통해 Azure와 동기화된 데이터는 수동 삭제 작업이 수행되거나 문제의 데이터가 유효하지 않은 것으로 판명될 때까지 무기한 보존됩니다. 

**명시적 삭제:** Azure 관리자가 사용자 또는 디렉터리를 삭제하거나 관리자가 데이터 삭제를 명시적으로 요청하는 경우 데이터가 삭제됩니다.

* **사용자 삭제**: 사용자가 Azure AD에서 삭제되면 사용자 계정 로밍 데이터는 삭제하도록 표시되고 90~180일 사이에 삭제됩니다. 
* **디렉터리 삭제**: Azure AD에서 전체 디렉터리를 삭제하면 그 즉시 디렉터리가 삭제됩니다. 해당 디렉터리와 연결된 모든 설정 데이터는 삭제하도록 표시되고 90~180일 사이에 삭제됩니다. 
* **요청 시 삭제**: Azure AD 관리자는 특정 사용자의 데이터 또는 설정 데이터를 수동으로 삭제하고 싶으면 [Azure 지원](https://azure.microsoft.com/support/)에서 티켓을 발행하면 됩니다. 

**유효하지 않은 데이터 삭제**: 1년(“보존 기간”)간 액세스되지 않은 데이터는 유효하지 않은 것으로 취급되어 Azure에서 삭제될 수 있습니다. 보존 기간은 변경될 수 있지만 90일 이상입니다. 특정 Windows/응용 프로그램 설정이 유효하지 않을 수도 있고 사용자의 모든 설정이 유효하지 않을 수도 있습니다. 예:

* 특정 설정 컬렉션에 아무 장치도 액세스하지 않는 경우(예: 장치에서 응용 프로그램이 제거되거나 "테마"와 같은 설정 그룹이 사용자의 모든 장치에 대해 비활성화되는 경우) 해당 컬렉션은 보존 기간이 끝난 후 유효하지 않은 것으로 취급되어 삭제될 수 있습니다. 
* 사용자가 모든 장치에서 설정 동기화를 끄면 설정 데이터 전체가 액세스되지 않으며, 해당 사용자의 모든 설정 데이터는 보존 기간이 끝난 후 유효하지 않은 것으로 취급되어 삭제될 수 있습니다. 
* Azure AD 디렉터리 관리자가 전체 디렉터리에 대해 엔터프라이즈 상태 로밍을 끄면 해당 디렉터리의 모든 사용자가 설정 동기화를 중지하게 되며, 모든 사용자의 모든 설정 데이터는 보존 기간이 끝난 후 유효하지 않은 것으로 취급되어 삭제될 수 있습니다. 

**삭제된 데이터 복구**: 데이터 보존 정책을 구성할 수 없습니다. 데이터가 영구적으로 삭제된 후에는 다시 복구할 수 없습니다. 그러나 설정 데이터는 Azure에서만 삭제되고 최종 사용자 장치에서는 삭제되지 않습니다. 나중에 다른 장치가 엔터프라이즈 상태 로밍 서비스에 다시 연결하면 설정이 다시 동기화되어 Azure에 저장됩니다.

## <a name="related-topics"></a>관련된 항목
* [엔터프라이즈 상태 로밍 개요](active-directory-windows-enterprise-state-roaming-overview.md)
* [설정 및 데이터 로밍 FAQ](active-directory-windows-enterprise-state-roaming-faqs.md)
* [설정 동기화에 대한 그룹 정책 및 MDM 설정](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Windows 10 로밍 설정 참조](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [문제 해결](active-directory-windows-enterprise-state-roaming-troubleshooting.md)



<!--HONumber=Jan17_HO1-->


