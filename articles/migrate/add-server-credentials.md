---
title: 소프트웨어 인벤토리, 종속성 및 SQL Server 인스턴스 및 데이터베이스를 검색 하기 위한 서버 자격 증명 제공
description: 어플라이언스 구성 관리자에서 서버 자격 증명을 제공 하는 방법을 알아봅니다.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: e72d509f71704bbf8608543df5e819a9b8783935
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562072"
---
# <a name="provide-server-credentials-to-discover-software-inventory-dependencies-and-sql-server-instances-and-databases"></a>소프트웨어 인벤토리, 종속성 및 SQL Server 인스턴스 및 데이터베이스를 검색 하기 위한 서버 자격 증명 제공

이 문서에 따라 어플라이언스 구성 관리자에서 소프트웨어 인벤토리 (설치 된 응용 프로그램 검색), 에이전트 없는 종속성 분석을 수행 하 고 SQL Server 인스턴스와 데이터베이스를 검색 하는 여러 서버 자격 증명을 추가 하는 방법을 알아봅니다.

> [!Note]
> VMware 환경에서 실행되는 SQL Server 인스턴스 및 데이터베이스를 검색하고 평가하는 기능은 현재 미리 보기로 제공됩니다. 이 기능을 사용해 보려면 [**이 링크**](https://aka.ms/AzureMigrate/SQL)를 사용하여 **오스트레일리아 동부** 지역에서 프로젝트를 만듭니다. 오스트레일리아 동부에 이미 프로젝트가 있고 이 기능을 사용해 보려는 경우 포털에서 이러한 [**필수 구성 요소**](how-to-discover-sql-existing-project.md)를 완료했는지 확인하세요.

[Azure Migrate 어플라이언스](migrate-appliance.md) 는 VMware 환경에서 실행 되는 온-프레미스 서버를 검색 하 고 Azure에 서버 구성 및 성능 메타 데이터를 전송 하기 위한 검색 및 평가 Azure Migrate에서 사용 하는 경량 어플라이언스입니다. 어플라이언스를 사용 하 여 소프트웨어 인벤토리, 에이전트 없는 종속성 분석 및 SQL Server 인스턴스 및 데이터베이스 검색을 수행할 수도 있습니다.

이러한 기능을 사용 하려는 경우 다음 단계를 수행 하 여 서버 자격 증명을 제공할 수 있습니다. 어플라이언스는 검색 기능을 수행 하기 위해 자격 증명을 서버에 자동으로 매핑하도록 시도 합니다.

## <a name="add-credentials-for-servers-running-in-vmware-environment"></a>VMware 환경에서 실행 중인 서버에 대 한 자격 증명 추가

### <a name="types-of-server-credentials-supported"></a>지원 되는 서버 자격 증명 유형

도메인, 비도메인 (Windows 또는 Linux) 또는 SQL Server 인증 자격 증명 일 수 있는 어플라이언스 구성 관리자에서 여러 서버 자격 증명을 추가할 수 있습니다.

지원 되는 서버 자격 증명 유형은 아래 표에 나와 있습니다.

자격 증명 유형 | Description
--- | ---
**도메인 자격 증명** | **자격 증명 추가** 모달 드롭다운의 옵션을 선택 하 여 **도메인 자격 증명** 을 추가할 수 있습니다. <br/><br/> 도메인 자격 증명을 제공 하려면 FQDN 형식으로 제공 되어야 하는 **도메인 이름을** 지정 해야 합니다 (예: prod.corp.contoso.com). <br/><br/> 또한 자격 증명, 사용자 이름 및 암호에 대 한 이름을 지정 해야 합니다. <br/><br/> 추가 된 도메인 자격 증명은 도메인의 Active Directory에 대 한 정품 인증을 자동으로 검사 합니다. 이는 기기가 검색 된 서버에 대해 도메인 자격 증명을 매핑하려고 시도할 때 계정 잠금을 방지 하기 위한 것입니다. <br/><br/> 어플라이언스는 유효성 검사에 실패 한 도메인 자격 증명을 매핑하려고 시도 하지 않습니다. 소프트웨어 인벤토리를 계속 진행 하려면 하나 이상의 도메인 자격 증명이 있거나 하나 이상의 도메인 자격 증명이 있어야 합니다.<br/><br/>Windows 서버에 대해 자동으로 매핑된 도메인 자격 증명은 소프트웨어 인벤토리를 수행 하는 데 사용 되며 _SQL server에서 windows 인증 모드를 구성한 경우_ 에도 SQL Server 인스턴스 및 데이터베이스를 검색 하는 데 사용할 수 있습니다.<br/> SQL Server에서 지원 되는 인증 모드의 유형에 [대해 자세히 알아보세요](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) .
**비 도메인 자격 증명 (Windows/Linux)** | **자격 증명 추가** 모달의 드롭다운에서 필요한 옵션을 선택 하 여 **Windows (** 비도메인) 또는 **Linux (비도메인)** 를 추가할 수 있습니다. <br/><br/> 자격 증명, 사용자 이름 및 암호에 대 한 친숙 한 이름을 지정 해야 합니다.
**SQL Server 인증 자격 증명** | **자격 증명 추가** 모달 드롭다운의 옵션을 선택 하 여 **SQL Server 인증** 자격 증명을 추가할 수 있습니다. <br/><br/> 자격 증명, 사용자 이름 및 암호에 대 한 친숙 한 이름을 지정 해야 합니다. <br/><br/> SQL Server에서 SQL Server 인증 모드를 구성한 경우이 유형의 자격 증명을 추가 하 여 VMware 환경에서 실행 중인 SQL Server 인스턴스 및 데이터베이스를 검색할 수 있습니다.<br/> SQL Server에서 지원 되는 인증 모드의 유형에 [대해 자세히 알아보세요](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) .<br/><br/> 인증서가 SQL Server 인증 자격 증명을 사용 하 여 SQL Server 인스턴스와 데이터베이스를 검색 하기 전에 어플라이언스에서 소프트웨어 인벤토리를 완료 하 여 서버에 설치 된 SQL을 검색할 수 있도록 성공적으로 확인 된 도메인 자격 증명 또는 하나 이상의 Windows (도메인이 아닌) 자격 증명을 제공 해야 합니다.

소프트웨어 인벤토리, 에이전트 없는 종속성 분석을 수행 하 고 SQL Server 인스턴스와 데이터베이스를 검색 하려면 Windows/Linux 자격 증명에 필요한 권한을 확인 합니다.

### <a name="required-permissions"></a>필요한 사용 권한

아래 표에는 해당 기능을 수행 하기 위해 어플라이언스에 제공 된 서버 자격 증명에 필요한 권한이 나와 있습니다.

기능 | Windows 자격 증명 | Linux 자격 증명
---| ---| ---
**소프트웨어 인벤토리** | 게스트 사용자 계정 | 일반/일반 사용자 계정 (비 sudo 액세스 권한)
**SQL Server 인스턴스 및 데이터베이스 검색** | Sysadmin 서버 역할의 구성원 인 사용자 계정입니다. | _현재 지원 되지 않음_
**에이전트 없는 종속성 분석** | 관리 권한이 있는 도메인 또는 비도메인 (로컬) 계정 | 루트 사용자 계정 또는 <br/> /bin/netstat 및/bin/ls 파일에 대 한 이러한 권한이 있는 계정: CAP_DAC_READ_SEARCH 및 CAP_SYS_PTRACE.<br/><br/> 다음 명령을 사용 하 여 이러한 기능을 설정 합니다. <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = ep/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = ep/bin/netstat

### <a name="recommended-practices-to-provide-credentials"></a>자격 증명을 제공 하는 권장 방법

- [필요한 사용 권한](add-server-credentials.md#required-permissions)으로 전용 도메인 사용자 계정을 만드는 것이 좋습니다 .이는 소프트웨어 인벤토리를 수행 하 고, 에이전트 없는 종속성 분석을 수행 하 고, 원하는 서버에서 SQL Server 인스턴스 및 데이터베이스를 검색 하도록 범위가 지정 됩니다.
- 소프트웨어 인벤토리를 시작 하려면 성공적으로 유효성을 검사 한 도메인 자격 증명 또는 하나 이상의 도메인 자격 증명을 제공 하는 것이 좋습니다.
- SQL 서버에서 Windows 인증 모드를 구성한 경우 도메인 자격 증명을 제공 하 SQL Server 인스턴스와 데이터베이스를 검색할 수 있습니다.
- SQL Server에 대 한 인증 모드를 SQL Server 구성한 경우에도 SQL Server 인증 자격 증명을 제공할 수 있지만, 어플라이언스에서 소프트웨어 인벤토리를 처음 완료할 수 있도록 하나 이상의 유효성을 검사 한 도메인 자격 증명 또는 하나 이상의 Windows (비도메인) 자격 증명을 제공 하는 것이 좋습니다.

## <a name="credentials-handling-on-appliance"></a>어플라이언스의 자격 증명 처리

- 어플라이언스 구성 관리자에서 제공 되는 모든 자격 증명은 어플라이언스 서버에 로컬로 저장 되 고 Azure로 보내지지 않습니다.
- 어플라이언스 서버에 저장 된 자격 증명은 DPAPI (데이터 보호 API)를 사용 하 여 암호화 됩니다.
- 자격 증명을 추가한 후 어플라이언스는 자격 증명을 자동으로 매핑하여 해당 서버에서 검색을 수행 하려고 시도 합니다.
- 어플라이언스는 자격 증명이 필요한 검색 데이터를 가져올 수 있을 때까지 모든 후속 검색 주기에 대해 서버에서 자동으로 매핑되는 자격 증명을 사용 합니다. 자격 증명의 작동이 중지 되 면 어플라이언스는 추가 된 자격 증명 목록에서 다시 매핑하여 서버에서 지속적인 검색을 계속 시도 합니다.
- 추가 된 도메인 자격 증명은 도메인의 Active Directory에 대 한 정품 인증을 자동으로 검사 합니다. 이는 기기가 검색 된 서버에 대해 도메인 자격 증명을 매핑하려고 시도할 때 계정 잠금을 방지 하기 위한 것입니다. 어플라이언스는 유효성 검사에 실패 한 도메인 자격 증명을 매핑하려고 시도 하지 않습니다.
- 어플라이언스에서 서버에 대해 도메인 또는 비도메인 자격 증명을 매핑할 수 없는 경우 프로젝트의 서버에 대해 "자격 증명을 사용할 수 없음" 상태가 표시 됩니다.

## <a name="next-steps"></a>다음 단계

[VMware 환경에서 실행 중인 서버 검색](tutorial-discover-vmware.md) 에 대 한 자습서를 검토 합니다.