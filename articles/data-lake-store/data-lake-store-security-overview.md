<properties 
   pageTitle="Data Lake 저장소의 보안에 대한 개요 | Microsoft Azure" 
   description="Azure Data Lake 저장소가 어떻게 보안 빅 데이터 저장소인지 이해" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# Azure Data Lake 저장소의 보호

많은 기업에서 현명한 의사 결정을 위한 비즈니스 통찰력을 얻는 데 빅 데이터 분석을 활용하고 있습니다. 이러한 조직은 다양한 사용자 수가 증가하는 복잡하고 규제된 환경을 포함할 수 있습니다. 따라서 기업은 중요한 비즈니스 데이터가 안전하게 저장되어 있고 관련 사용자에게 적절한 수준의 액세스 권한이 부여되도록 해야 합니다. Azure Data Lake 저장소는 이러한 보안 요구 사항을 염두에 두고 설계되었습니다. 이 문서에서는 다음과 같은 Azure Data Lake 저장소(ADLS)의 보안 기능에 대해 알아봅니다.

* 인증
* 권한 부여
* 네트워크 격리
* 데이터 보호
* 감사

 
## 인증 및 ID 관리

인증은 사용자가 Data Lake 저장소와 상호 작용하거나 서비스가 Data Lake 저장소에 연결될 때 사용자가 자신의 ID를 증명하는 프로세스입니다. ID 관리 및 인증을 위해 Data Lake 저장소는 AAD([Azure Active Directory](../active-directory/active-directory-whatis.md))와 사용자 및 그룹의 관리를 간소화하는 포괄적인 ID 및 액세스 관리 클라우드 솔루션을 사용합니다.

현재 모든 Azure 구독은 Azure Active Directory와 연결할 수 있습니다. 이 디렉터리에 정의된 사용자 및 서비스 ID만 Azure 포털, 명령줄 도구 또는 Data Lake 저장소 SDK를 사용하여 작성된 클라이언트 응용 프로그램을 사용하여 Data Lake 저장소에 액세스할 수 있습니다. 중앙 집중식 액세스 제어 메커니즘으로 Azure Active Directory를 사용하는 주요 이점은 다음과 같습니다.

* ID 수명 주기 관리를 단순화합니다. 디렉터리에서 계정을 삭제하거나 비활성화하여 사용자 또는 서비스(서비스 주체 ID)를 신속하게 만들고 해지할 수 있습니다.

* 사용자 로그인 및 트랜잭션에 대해 추가 보안 계층을 제공하는 [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)을 지원합니다.

* OAuth 및 OpenID와 같은 개방형 표준 프로토콜을 사용하여 클라이언트에서 사용자를 인증할 수 있도록 합니다.

* 엔터프라이즈 디렉터리 서비스 및 클라우드 ID 공급자와 페더레이션을 사용할 수 있습니다.

## 권한 부여 및 액세스 제어

사용자가 Azure Data Lake 저장소에 액세스하도록 AAD에 의해 인증된 경우 권한 부여로 Data Lake 저장소에 대한 액세스 권한을 제어합니다. Data Lake Store는 다음과 같은 방식으로 계정 관련 작업과 데이터 관련 작업에 대한 권한 부여를 구분합니다.

* 계정을 관리하기 위해 Azure에서 제공하는 RBAC([역할 기반 액세스 제어](../active-directory/role-based-access-control-what-is.md))
* 저장소에서 데이터에 액세스하기 위한 POSIX ACL

### 계정 관리를 위해 RBAC 사용

기본적으로 네 가지 기본 역할이 정의되어 있습니다. 포털, PowerShell cmdlet 및 REST API를 통해 Data Lake 저장소 계정에서 다양한 작업이 허용됩니다. **소유자** 및 **참여자** 역할은 해당 계정에 다양한 관리 기능을 허용합니다. 데이터와 상호 작용하는 사용자의 경우 **리더** 역할에 추가할 수 있습니다.

![RBAC 역할](./media/data-lake-store-security-overview/rbac-roles.png "RBAC 역할")

이러한 역할 할당의 목적은 계정 관리를 위한 것이며 특정 역할에 따라 데이터 액세스 권한에 영향이 있을 수 있습니다. 파일 시스템에 대해 사용자가 수행할 수 있는 작업의 경우 액세스 제어 목록(ACL)을 사용해야 합니다. 다음은 관리 권한 및 이러한 역할에 대한 데이터 액세스 권한을 요약한 것입니다.

| 역할 | 관리 권한 | 데이터 액세스 권한 | 설명 |
|--------------------------|---------------------------------|--------------------|-------------|
| 할당된 역할 없음 | 없음 | ACL에 의해 제어 | 이러한 경우 사용자가 Azure 포털 또는 Azure PowerShell Cmdlet을 사용하여 Data Lake 저장소를 찾아볼 수 없습니다. 이러한 사용자는 명령줄 도구에 전적으로 의존해야 합니다. |
| 소유자 | 모두 | 모두 | 소유자는 superuser이므로 소유자 역할을 통해 모든 항목을 관리할 수 있고 데이터에 대한 전체 액세스 권한을 보유합니다. | 
| 판독기 | 읽기 전용 | ACL에 의해 제어 | 판독기는 계정 관리와 관련된 모든 항목(예: 어떤 사용자가 어떤 역할에 할당되는지)을 볼 수 있으나 변경할 수는 없습니다. |
| 참여자 | 역할 추가 및 제거를 제외한 모든 항목 | ACL에 의해 제어 | 참여자는 경고 만들기/관리, 배포 등 계정의 다른 측면을 관리할 수 있습니다. 참여자는 역할을 추가 또는 제거할 수 없습니다. |
| 사용자 액세스 관리자 | 역할 추가 및 제거 | ACL에 의해 제어 | 사용자 액세스 관리자를 통해 계정에 대한 사용자 액세스를 관리할 수 있습니다. |

자세한 내용은 [사용자 또는 보안 그룹을 Azure Data Lake Store 계정에 할당](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts)을 참조하세요.

### 파일 시스템 작업에 ACL 사용

Azure Data Lake Store는 HDFS와 같은 계층적 파일 시스템이며 [POSIX ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists)을 지원하여 소유자, 소유 그룹 및 기타 사용자/그룹에 부여된 리소스에 대한 읽기(r), 쓰기(w) 및 실행(x) 액세스 권한을 허용합니다. Data Lake Store 공개 미리 보기(현재 릴리스)에서 ACL은 루트 폴더, 하위 폴더뿐만 아니라 개별 파일에서도 사용할 수 있습니다. 루트 폴더에 적용할 ACL는 모든 자식 폴더/파일에도 적용할 수 있습니다.

[보안 그룹](../active-directory/active-directory-accessmanagement-manage-groups.md)을 사용하여 많은 사용자에 대해 ACL을 정의하는 것이 좋습니다. 사용자를 보안 그룹으로 그룹화한 후 파일 및 폴더에 대한 ACL을 해당 보안 그룹에 할당합니다. 사용자 지정 액세스의 일환으로 최대 9개의 항목만 추가할 수 있는 제한이 있으므로 사용자 지정 액세스를 제공할 때 유용합니다. AAD 보안 그룹을 사용하여 Data Lake Store에 저장된 데이터를 보호하는 방법에 대한 자세한 내용은 [ACL인 사용자 또는 보안 그룹을 Azure Data Lake Store 파일 시스템에 할당](data-lake-store-secure-data.md#filepermissions)을 참조하세요.

![표준 및 사용자 지정 액세스 나열](./media/data-lake-store-security-overview/adl.acl.2.png "표준 및 사용자 지정 액세스 나열")

## 네트워크 격리

Azure Data Lake 저장소를 사용하면 데이터 저장소에 대한 액세스를 네트워크 수준에서 추가로 잠글 수 있습니다. 방화벽을 설정하고 신뢰할 수 있는 클라이언트에 대한 IP 주소 범위를 정의할 수 있습니다. 사용하도록 설정되면 정의된 범위 내에 있는 IP 주소를 보유한 클라이언트만 저장소에 연결할 수 있습니다.

![방화벽 설정 및 IP 액세스](./media/data-lake-store-security-overview/firewall-ip-access.png "방화벽 설정 및 IP 주소")

## 데이터 보호

조직에서는 수명 주기 내내 비즈니스에 중요한 데이터가 보호되는지 확인하려고 합니다. 전송 중인 데이터의 경우 Data Lake 저장소는 업계 표준 TLS(전송 계층 보안 프로토콜)을 사용하여 클라이언트 및 Data Lake 보안 간 데이터를 보호합니다.

미사용 데이터에 대한 데이터 보호는 향후 릴리스에서 제공될 예정입니다.

## 감사 및 진단 로그

관리 관련 활동 또는 데이터 관련 활동에 대한 로그를 찾는지에 따라 감사 또는 진단 로그를 사용할 수 있습니다.

*  관리 관련 활동에서는 Azure Resource Manager API를 사용하며 감사 로그를 통해 Azure 포털에 표시됩니다.
*  데이터 관련 활동에서는 WebHDFS API를 사용하고 진단 로그를 통해 Azure 포털에 표시됩니다.

### 감사 로그

규정을 준수하기 위해 인시던트를 자세히 살펴보아야 하는 경우 조직에 적절한 감사 내역이 필요할 수 있습니다. Data Lake 저장소에는 기본 제공 모니터링 및 감사가 있으며 여기서 모든 계정 관리 활동을 기록합니다.

계정 관리 감사 내역의 경우 로그에 대해 관심이 있는 열을 보고 선택한 후 감사 로그를 Azure 저장소에 내보낼 수도 있습니다.

![감사 로그](./media/data-lake-store-security-overview/audit-logs.png "감사 로그")

### 진단 로그

Azure 포털에서 데이터 액세스 감사 내역을 사용하도록 설정(**진단 설정**)하고 로그가 저장될 Azure Blob 저장소 계정을 제공할 수 있습니다.

![진단 로그](./media/data-lake-store-security-overview/diagnostic-logs.png "진단 로그")

진단 설정을 사용하도록 설정했으면 **진단 로그** 탭에서 로그를 볼 수 있습니다.

Azure Data Lake Store와 함께 진단 로그를 사용하는 방법에 대한 자세한 내용은 [Data Lake Store에 대한 진단 로그 액세스](data-lake-store-diagnostic-logs.md)를 참조하세요.

## 요약

엔터프라이즈 고객은 안전하고 사용하기 간편한 데이터 분석 클라우드 플랫폼을 요구합니다. Azure Data Lake 저장소는 Azure Active Direction 통합, ACL 기반 권한 부여, 네트워크 격리, 전송 중 및 미사용 데이터 암호화(향후 제공) 및 감사를 통해 ID 관리 및 인증으로 이러한 요구 사항을 해결하도록 설계되었습니다.

Data Lake Store에 포함된 새로운 기능을 참조하려는 경우 [Uservoice 포럼](https://feedback.azure.com/forums/327234-data-lake)에 사용자 의견을 보내 주세요.

## 참고 항목

- [Azure 데이터 레이크 저장소 개요](data-lake-store-overview.md)
- [Data Lake 저장소 시작](data-lake-store-get-started-portal.md)
- [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)

<!---HONumber=AcomDC_0803_2016-->