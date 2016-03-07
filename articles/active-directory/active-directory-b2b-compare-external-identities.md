<properties
   pageTitle="Azure Active Directory를 사용하여 외부 ID 관리 기능 비교 | Microsoft Azure"
   description="외부 ID에 대한 인증 및 권한 부여를 지원하기 위해 Azure Active Directory B2B 공동 작업, B2C 및 다중 테넌트 앱을 비교합니다."
   services="active-directory"
   documentationCenter="" 
   authors="arvindsuthar"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="02/24/2016"
   ms.author="asuthar"/>

# Azure Active Directory를 사용하여 외부 ID 관리 기능 비교

Azure AD(Azure Active Directory)는 모바일 인력의 SaaS 앱 액세스를 관리하는 것 외에도 조직이 비즈니스 파트너와 리소스를 공유하고 기업 및 소비자에게 응용 프로그램을 제공하도록 지원합니다.

## 기업을 위한 응용 프로그램 개발

급여 서비스와 같은 서비스 또는 응용 프로그램을 기업에 제공하시나요? Azure AD는 Azure AD를 Office 365 또는 기타 엔터프라이즈 서비스 배포의 일부로 이미 구성한 수백만 조직과 원활하게 통합되는 응용 프로그램을 빌드할 수 있는 ID 플랫폼을 제공합니다.

**예:** 제약 배포자는 의료 산업에 의료 공급 장치 및 정보 시스템을 제공합니다. 이들은 의료 실무에 분석 응용 프로그램을 제공해야 하고 고객이 자신의 ID를 관리하기를 바랍니다. 이 회사는 실무 관리 응용 프로그램을 위한 ID 플랫폼으로 Azure AD를 선택했으며 필요한 경우 자신의 고객들에게 등록 시 Azure AD ID를 제공합니다. 자세한 내용은 [Azure Active Directory 개발자 가이드](active-directory-developers-guide.md)를 참조하세요.

## 회사 리소스에 비즈니스 파트너 액세스 사용 설정

SharePoint 사이트 또는 ERP 시스템과 같은 엔터프라이즈 리소스에 액세스해야 하는 비즈니스 파트너나 회사 외부인이 있나요? Azure AD를 사용하면 관리자가 외부 사용자(Azure AD에 있거나 없을 수 있음)에게 회사 응용 프로그램에 대한 Single Sign On 액세스를 부여할 수 있습니다. 따라서 조직 내 액세스 정책을 제어하는 동안 사용자가 파트너 조직을 떠날 때 액세스 권한을 잃게 되므로 보안이 강화됩니다. 또한 외부 파트너 디렉터리나 파트너당 페더레이션 관계를 관리할 필요가 없으므로 관리를 간소화합니다.

**예:** 이미징 회사는 판매점에 사진 이미징 서비스를 제공하고 규모가 가장 큰 인쇄 키오스크 소매 네트워크를 운영합니다. 이들은 소매 비즈니스 파트너의 수천 명의 사용자가 자신만의 고유한 자격 증명을 사용하여 최신 파트너 시장 자료를 다운로드하고 회사의 공급 업체 엑스트라넷에서 사진 처리 소모품을 추가 주문할 수 있도록 하는 데 Azure AD를 선택했습니다. 자세한 내용은 [Azure AD B2B 공동 작업](active-directory-b2b-what-is-azure-ad-b2b.md)을 참조하세요.

## 소비자를 위한 응용 프로그램 개발

수백만 소비자에게 소매 상점처럼 안전하게 비용 효율적으로 온라인 응용 프로그램을 게시해야 하나요? Azure AD는 응용 프로그램 소비자를 위해 소셜은 물론 사용자 이름/암호 로그인, 브랜드 셀프 서비스 등록 및 셀프 서비스 암호 재설정을 설정할 수 있는 플랫폼을 제공합니다. 따라서 개발자의 작업은 줄어들고 소비자의 편의성은 증가합니다.

**예:** 세계 최고 스포츠 프랜차이즈는 4억 5천만 팬을 직접 연계해야 합니다. 이를 위해 Azure AD를 사용하여 사용자 인증 및 프로필 저장소용으로 모바일 앱을 구축했습니다. 팬은 Facebook과 같은 소셜 계정을 사용하여 간편하게 등록 및 로그인하며 iOS, Android, Windows Phone에 구애받지 않고 원활한 환경에서 기존의 사용자 이름/암호를 사용할 수 있습니다. 설정된 Azure AD 플랫폼을 구축하면 사용자 지정 코드가 크게 줄어들어 프랜차이즈에 사용자 지정된 브랜딩을 제공하고 보안, 데이터 침해 및 확장성에 대한 우려가 해소됩니다. 자세한 내용은 [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)를 참조하세요.

## Azure AD 기능 비교

이 문서에서 이미 설명한 각 시나리오는 Azure AD 내의 기능으로 해결됩니다. 이 테이블을 통해 자신에게 가장 관련된 기능을 명확히 할 수 있습니다.

| **고려할 제품** | [Azure AD 다중 테넌트 SaaS 앱](active-directory-developers-guide.md) | [Azure AD B2B 공동 작업](active-directory-b2b-what-is-azure-ad-b2b.md) | [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/) |
|-----------------------|-------------------------|----------------------------|------------------------|
| **제공해야 하는 기능** | 기업에 대한 서비스 | 내 앱에 대한 파트너 액세스 | 소비자에 대한 서비스 |
| **유사 업종** | 제약 배포자 | 이미징 회사 | 스포츠 프랜차이즈 |
| **앱 배포 대상** | 실무 관리 | 공급 업체 엑스트라넷 | 축구 팬 |
| **대상 지정** | 의사 사무실 | 승인된 비즈니스 파트너 | 전자 메일에 있는 임의 사용자 |
| **액세스 방법** | 고객 관리자 동의 | 내 관리자 초대 | 소비자 등록 |

<!---HONumber=AcomDC_0224_2016-->