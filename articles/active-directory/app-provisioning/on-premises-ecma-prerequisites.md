---
title: Azure AD ECMA 커넥터 호스트의 필수 구성 요소
description: 이 문서에서는 Azure AD ECMA 커넥터 호스트를 사용하는 데 필요한 필수 구성 요소 및 하드웨어 요구 사항을 설명합니다.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c668380103088fb0f434d513cf7490c39e4ead2
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570253"
---
# <a name="prerequisites-for-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA 커넥터 호스트의 필수 구성 요소

>[!IMPORTANT]
> 온-프레미스 프로비저닝 미리 보기는 현재 초대 전용 미리 보기로 제공됩니다. [여기](https://aka.ms/onpremprovisioningpublicpreviewaccess)에서 기능에 대한 액세스를 요청할 수 있습니다. 일반 공급을 준비 중이므로 향후 몇 개월 동안 더 많은 고객 및 커넥터에 대한 미리 보기를 공개할 예정입니다.

이 문서에서는 Azure AD ECMA 커넥터 호스트를 사용하는 데 필요한 필수 구성 요소에 대한 지침을 제공합니다.  

Azure AD ECMA 커넥터 호스트 설치 및 구성은 프로세스입니다. 다음 흐름을 사용하여 프로세스를 안내할 수 있습니다.

 ![설치 흐름](./media/on-premises-ecma-prerequisites/flow-1.png)  

설치 및 구성에 대한 자세한 내용은 다음을 참조하세요.
   - [Azure AD ECMA 커넥터 호스트 설치](on-premises-ecma-install.md)
   - [Azure AD ECMA 커넥터 호스트 및 프로비저닝 에이전트 구성](on-premises-ecma-configure.md)
   - [Azure AD ECMA 커넥터 호스트 일반 SQL 커넥터 구성](on-premises-sql-connector-configure.md)

## <a name="on-premises-pre-requisites"></a>온-프레미스 필수 구성 요소
 - 사용자가 만들고, 업데이트하고, 삭제할 수 있는 SQL 데이터베이스와 같은 대상 시스템입니다.
 - 내보내기, 스키마 검색 및 선택적으로 전체 가져오기 또는 델타 가져오기 작업을 지원하는 대상 시스템에 대한 ECMA 2.0 이상 커넥터입니다. 구성하는 동안 ECMA 커넥터가 준비되지 않은 경우 환경에 SQL Server가 있고 일반 SQL 커넥터를 사용하는 경우에도 엔드투엔드 흐름의 유효성을 검사할 수 있습니다.
 - 인터넷에 액세스할 수 있는 TCP/IP 주소를 사용하는 Windows Server 2016 이상 컴퓨터, 대상 시스템에 대한 연결 및 login.microsoftonline.com에 대한 아웃바운드 연결(예: Azure IaaS 또는 프록시 뒤에서 호스팅되는 Windows Server 2016 가상 컴퓨터). 이 서버에는 3GB 이상의 RAM이 있어야 합니다.
 - .NET Framework 4.7.1을 사용하는 컴퓨터

## <a name="cloud-requirements"></a>클라우드 요구 사항

 - Azure AD Premium P1 또는 Premium P2(또는 EMS E3 또는 E5)를 사용하는 Azure AD 테넌트. 
    [!INCLUDE [active-directory-p1-license.md](../../../includes/active-directory-p1-license.md)]

 - Azure Portal에서 프로비저닝을 구성하기 위한 프로비저닝 에이전트와 애플리케이션 관리자 또는 클라우드 관리자 역할을 구성하기 위한 하이브리드 관리자 역할입니다.


## <a name="next-steps"></a>다음 단계

- [Azure AD ECMA 커넥터 호스트 설치](on-premises-ecma-install.md)
- [Azure AD ECMA 커넥터 호스트 구성](on-premises-ecma-configure.md)
- [일반 SQL 커넥터](on-premises-sql-connector-configure.md)
- [자습서: ECMA 커넥터 호스트 일반 SQL 커넥터](tutorial-ecma-sql-connector.md)
