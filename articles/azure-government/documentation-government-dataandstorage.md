<properties
	pageTitle="Azure Government 설명서 | Microsoft Azure"
	description="Azure Government용 응용 프로그램 개발에 대한 지침 및 기능 비교를 제공합니다."
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="08/25/2016"
	ms.author="ryansoc"/>


#  Azure Government 데이터 및 저장소

##  저장소

다음 정보는 Azure 저장소에 대한 Azure Government 경계를 식별합니다.

| 규제된/제어된 데이터 허용됨 | 규제된/제어된 데이터 허용되지 않음 |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure 저장소 제품 내에서 입력, 저장 및 처리된 데이터는 내보내기 제어된 데이터를 포함할 수 있습니다. Azure 플랫폼 구성 요소에 대한 액세스를 위한 암호 및 스마트 카드 PIN과 같은 정적 인증자. Azure 플랫폼 구성 요소를 관리하는 데 사용되는 인증서의 개인 키. Azure 서비스에 저장된 인증서, 암호화 키, 마스터 키 및 저장소 키와 같은 기타 보안 정보/암호. | Azure 저장소 메타데이터는 내보내기 제어된 데이터를 포함하도록 허용되지 않습니다. 이 메타데이터는 저장소 제품을 생성 및 유지 관리하는 경우 입력된 모든 구성 데이터를 포함합니다. 다음 필드에 규제된/제어된 데이터인 리소스 그룹, 배포 이름, 리소스 이름, 리소스 태그를 입력하지 마세요.  

추가 정보는 <a href=https://azure.microsoft.com/ko-KR/documentation/services/storage/> Azure 가상 컴퓨터 공용 설명서 </a>을(를) 참조하세요.

추가 정보 및 업데이트는 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.

##  SQL 데이터베이스

다음 정보는 Azure 저장소에 대한 Azure Government 경계를 식별합니다.

| 규제된/제어된 데이터 허용됨 | 규제된/제어된 데이터 허용되지 않음 |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Microsoft Azure SQL에서 저장되고 처리된 모든 데이터는 Azure Government 규제된 데이터를 포함할 수 있습니다. Azure Government 규제된 데이터의 데이터 전송을 위해 데이터베이스 도구를 사용해야 합니다. | Azure SQL 메타데이터는 내보내기 제어된 데이터를 포함하도록 허용되지 않습니다. 이 메타데이터는 저장소 제품을 생성 및 유지 관리하는 경우 입력된 모든 구성 데이터를 포함합니다. 다음 필드에 규제된/제어된 데이터인 데이터베이스 이름, 구독 이름, 리소스 그룹, 서버 이름, 서버 도메인 로그인, 배포 이름, 리소스 이름, 리소스 태그를 입력하지 마세요.

SQL 데이터베이스 v11은 일반적으로 Azure Government에서 사용할 수 있습니다.

메타데이터 표시 여부 구성에 대한 추가 지침 및 보호 모범 사례는 <a href="https://msdn.microsoft.com/ko-KR/library/bb510589.aspx">SQL 데이터베이스 엔진 Microsoft 보안 센터</a> 및 <a href="https://azure.microsoft.com/ko-KR/documentation/services/sql-database/">Azure SQL 데이터베이스 공용 설명서</a>를 참조하세요.

추가 정보 및 업데이트는 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.

<!---HONumber=AcomDC_0831_2016-->