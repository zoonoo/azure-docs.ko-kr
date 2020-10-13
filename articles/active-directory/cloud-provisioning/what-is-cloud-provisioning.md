---
title: Azure AD Connect 클라우드 프로비저닝이란? | Microsoft Docs
description: Azure AD Connect 클라우드 프로비저닝을 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a5a69bedb45f8037f3a97f5ebf4e2128930330a
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91728355"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>Azure AD Connect 클라우드 프로비저닝이란?
Azure AD Connect 클라우드 프로비저닝은 Azure AD에 대한 사용자, 그룹 및 연락처의 동기화를 위해 하이브리드 ID 목표를 충족하고 달성하기 위해 설계된 새로운 Microsoft 에이전트입니다.  Azure AD Connect 동기화와 함께 사용할 수 있으며 다음과 같은 이점을 제공합니다.
    
- 다중 포리스트 연결 끊김 Active Directory 포리스트 환경에서 Azure AD 테넌트와 동기화를 지원합니다. 일반적인 시나리오에는 획득한 회사의 AD 포리스트가 부모 회사의 AD 포리스트와 이전에 여러 AD 포리스트가 있는 회사에서 격리되는 합병 & 획득이 포함됩니다.
- 경량 프로비저닝 에이전트로 간소화된 설치: 에이전트는 클라우드에서 관리되는 모든 동기화 구성과 함께 AD에서 Azure AD로의 브리지 역할을 합니다. 
- 여러 프로비저닝 에이전트를 사용하여 고가용성 배포를 간소화할 수 있습니다. 특히 AD에서 Azure AD로의 암호 해시 동기화를 사용하는 조직에 특히 중요합니다.


![Azure AD Connect의 정의](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Azure AD Connect 클라우드 프로비저닝은 Azure AD Connect 동기화와 어떻게 다른가요?
Azure AD Connect 클라우드 프로비저닝을 사용하여 AD에서 Azure AD로의 프로비저닝은 Microsoft Online Services에서 오케스트레이션됩니다. 조직에서는 Azure AD와 AD 간의 브리지 역할을 하는 경량 에이전트로 온-프레미스 및 IaaS 호스팅 환경에만 배포하면 됩니다. 프로비저닝 구성은 Azure AD에 저장되고 서비스의 일부로 관리됩니다.

## <a name="azure-ad-connect-cloud-provisioning-video"></a>Azure AD Connect 클라우드 프로비저닝 비디오
다음 짧은 비디오는 Azure AD Connect 클라우드 프로비저닝에 대한 훌륭한 개요를 제공합니다.

> [!VIDEO https://youtube/embed/mOT3ID02_YQ]


## <a name="comparison-between-azure-ad-connect-and-cloud-provisioning"></a>Azure AD Connect와 클라우드 프로비저닝 비교

다음 표에서는 Azure AD Connect와 Azure AD Connect 클라우드 프로비저닝을 비교합니다.

| 기능 | Azure Active Directory Connect 동기화| Azure Active Directory Connect 클라우드 프로비저닝 |
|:--- |:---:|:---:|
|단일 온-프레미스 AD 포리스트에 연결|● |● |
| 여러 온-프레미스 AD 포리스트에 연결 |● |● |
| 다중 연결 끊김 온-프레미스 AD 포리스트에 연결 | |● |
| 경량 에이전트 설치 모델 | |● |
| 고가용성을 위한 여러 활성 에이전트 | |● |
| LDAP 디렉터리에 연결|●| | 
| 사용자 개체에 대한 지원 |● |● |
| 그룹 개체에 대한 지원 |● |● |
| 연락처 개체에 대한 지원 |● |● |
| 디바이스 개체에 대한 지원 |● | |
| 특성 흐름에 대한 기본 사용자 지정 허용 |● |● |
| Exchange Online 특성 동기화 |● |● |
| 확장 특성 1-15 동기화 |● |● |
| 고객 정의 AD 특성(디렉터리 확장) 동기화 |● | |
| 암호 해시 동기화에 대한 지원 |●|●|
| 통과 인증에 대한 지원 |●||
| 페더레이션에 대한 지원 |●|●|
| 원활한 Single Sign-On|● |●|
| 도메인 컨트롤러에서 설치 지원 |● |● |
| Windows Server 2012 및 Windows Server 2012 R2 지원 |● |● |
| 도메인/OU/그룹에 대한 필터링 |● |● |
| 개체의 특성 값에 대한 필터링 |● | |
| 최소 특성 집합이 동기화되도록 허용(MinSync) |● |● |
| AD에서 Azure AD로의 흐름에서 특성 제거 허용 |● |● |
| 특성 흐름에 대한 고급 사용자 지정 허용 |● | |
| 쓰기 저장에 대한 지원(암호, 디바이스, 그룹) |● | |
| Azure AD Domain Services 지원|● | |
| [Exchange 하이브리드 쓰기 저장](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| AD 도메인당 5만 개 이상의 개체에 대한 지원 |● | |

## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [클라우드 프로비저닝 설치](how-to-install.md)
