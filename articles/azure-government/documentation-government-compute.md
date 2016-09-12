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


#  Azure 정부 계산

##  가상 컴퓨터


다음 정보는 Azure 가상 컴퓨터에 대한 Azure Government 경계를 식별합니다.

| 규제된/제어된 데이터 허용됨 | 규제된/제어된 데이터 허용되지 않음 |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| VM 내에서 입력, 저장 및 처리된 데이터는 내보내기 제어된 데이터를 포함할 수 있습니다. Azure 가상 컴퓨터 내에서 실행되는 이진 파일. Azure 플랫폼 구성 요소에 대한 액세스를 위한 암호 및 스마트 카드 PIN과 같은 정적 인증자. Azure 플랫폼 구성 요소를 관리하는 데 사용되는 인증서의 개인 키. SQL 연결 문자열. Azure 서비스에 저장된 인증서, 암호화 키, 마스터 키 및 저장소 키와 같은 기타 보안 정보/암호. | 메타데이터는 내보내기 제어된 데이터를 포함하도록 허용되지 않습니다. 이 메타데이터는 Azure 가상 컴퓨터를 생성 및 유지 관리하는 경우 입력된 모든 구성 데이터를 포함합니다. 다음 필드에 규제된/제어된 데이터인 테넌트 역할 이름, 리소스 그룹, 배포 이름, 리소스 이름, 리소스 태그를 입력하지 마세요.  

추가 정보는 <a href=https://azure.microsoft.com/ko-KR/documentation/services/virtual-machines/windows/> Azure 가상 컴퓨터 공용 설명서 </a>을(를) 참조하세요.

추가 정보 및 업데이트는 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.

<!---HONumber=AcomDC_0831_2016-->