<properties
    pageTitle="그룹 설정 구성을 위한 Azure Active Directory cmdlets | Microsoft Azure"
    description="Azure Active Directory cmdlets를 사용하여 그룹 설정을 관리하는 방법입니다."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="curtand"/>



# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>그룹 설정을 구성하는 Azure Active Directory cmdlets

디렉터리에서 구성할 수 있는 통합 그룹 설정:

1.  분류: 사용자가 그룹을 설정할 수 있는 쉼표로 구분한 분류 목록입니다. 예: “Classified”, “Secret”, “Top Secret.”

2.  사용 지침 URL: 조직에서 정의한 대로 사용자에게 통합된 그룹 사용 조건을 안내하는 URL입니다. 이 URL은 사용자가 그룹을 사용하는 사용자 인터페이스에 표시됩니다.

3.  그룹 만들기 활성화: 모든 사용자가 통합 그룹을 만들도록 허용하지 않거나, 일부 또는 모든 사용자가 통합 그룹을 만들도록 허용합니다. ON으로 설정되면 모든 사용자가 그룹을 만들 수 있습니다. OFF로 설정되면 모든 사용자가 그룹을 만들 수 없습니다. OFF로 설정했을 때, 사용자가 그룹을 만들도록 허용된 보안 그룹을 지정할 수 있습니다.

이 설정은 설정 및 설정 템플릿 개체를 사용하여 구성됩니다. 처음에는 디렉터리에 설정 개체가 보이지 않습니다. 즉, 디렉터리가 기본 설정으로 구성됩니다. 기본 설정을 변경하려면 설정 템플릿을 사용하여 새 설정 개체를 만듭니다. 설정 템플릿은 Microsoft가 정의합니다.

[Microsoft Connect 사이트](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)에서 이 작업에 사용된 cmdlets를 포함한 모듈을 다운로드할 수 있습니다.

## <a name="create-settings-at-the-directory-level"></a>디렉터리 수준에서 설정 만들기

다음 단계는 디렉터리 수준에서 설정을 만드는 것입니다. 이 설정은 디렉터리에 있는 모든 Office 그룹에 적용됩니다.

1. 어느 SettingTemplate을 사용할지 알 수 없는 경우, 다음 cmdlet이 설정 템플릿 목록을 반환합니다.

    `Get-MsolAllSettingTemplate`

    ![설정 템플릿 목록](./media/active-directory-accessmanagement-groups-settings-cmdlets/list-of-templates.png)

2. 사용 지침 URL을 추가하려면 사용 지침 URL 값을 정의하는 SettingsTemplate 개체를 가져와야 합니다. 즉, Group.Unified 템플릿입니다.

    `$template = Get-MsolSettingTemplate –TemplateId 62375ab9-6b52-47ed-826b-58e47e0e304b`

3. 다음에는 위 템플릿에 기초하여 새 설정 개체를 만듭니다.

    `$setting = $template.CreateSettingsObject()`

4. 그런 다음 사용 지침 값을 업데이트합니다.

    `$setting["UsageGuidelinesUrl"] = "<https://guideline.com>"`

5. 마지막으로 설정을 적용합니다.

    `New-MsolSettings –SettingsObject $setting`

    ![사용 지침 URL 추가](./media/active-directory-accessmanagement-groups-settings-cmdlets/add-usage-guideline-url.png)

다음은 Group.Unified 설정 템플릿에서 정의된 설정입니다.

 **설정**                          | **설명**                                                                                             
--------------------------------------|-----------------------------------------------
 <ul><li>ClassificationList<li>형식: String<li>기본값: “”                  | 통합 그룹에 적용할 수 있는 유효한 분류 값을 쉼표로 구분한 목록입니다.                
 <ul><li>EnableGroupCreation<li>형식: Boolean<li> 기본값: True              | 디렉터리에서 통합 그룹 만들기가 허용되는지 나타내는 플래그입니다.                               
 <ul><li>GroupCreationAllowedGroupId<li>형식: String<li>기본값: “”         | EnableGroupCreation == false일 때도 통합 그룹을 만들도록 허용된 보안 그룹의 GUID입니다.
 <ul><li>UsageGuidelinesUrl<li>형식: String<li>기본값: “”                  | 그룹 사용 지침 링크입니다.                                                                       

## <a name="read-settings-at-the-directory-level"></a>디렉터리 수준에서 설정 읽기

다음 단계는 디렉터리 수준에서 설정을 읽는 것입니다. 이 설정은 디렉터리에 있는 모든 Office 그룹에 적용됩니다.

1. 모든 기존 디렉터리 설정 읽기:

    `Get-MsolAllSettings`

2. 특정 그룹의 모든 설정 읽기:

    `Get-MsolAllSettings -TargetType Groups -TargetObjectId <groupObjectId>`

3. SettingId GUID를 사용하여 특정 디렉터리 설정 읽기:

    `Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

    ![설정 ID GUID](./media/active-directory-accessmanagement-groups-settings-cmdlets/settings-id-guid.png)

## <a name="update-settings-at-the-directory-level"></a>디렉터리 수준에서 설정 업데이트

다음 단계는 디렉터리 수준에서 설정을 업데이트하는 것입니다. 이 설정은 디렉터리에 있는 모든 Office 그룹에 적용됩니다.

1. 기존 설정 개체를 가져옵니다.

    `$setting = Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

2. 업데이트하려는 값을 가져옵니다.

    `$value = $Setting.GetSettingsValue()`

3. 값을 업데이트합니다.

    `$value["AllowToAddGuests"] = "false"`

4. 설정을 업데이트합니다.

    `Set-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c –SettingsValue $value`

## <a name="remove-settings-at-the-directory-level"></a>디렉터리 수준에서 설정 제거

다음 단계는 디렉터리 수준에서 설정을 제거하는 것입니다. 이 설정은 디렉터리에 있는 모든 Office 그룹에 적용됩니다.

    `Remove-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

## <a name="cmdlet-syntax-reference"></a>Cmdlet 구문 참조

[Azure Active Directory Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=808260)에서 더 많은 Azure Active Directory PowerShell 설명서를 찾을 수 있습니다.

## <a name="settingstemplate-object-reference-(group.unified-settingstemplate-object)"></a>SettingsTemplate 개체 참조(Group.Unified SettingsTemplate 개체)

- "name": "EnableGroupCreation", "type": "System.Boolean", "defaultValue": "true", "description": "통합 그룹 만들기 기능이 켜져 있는지 나타내는 부울 플래그."

- "name": "GroupCreationAllowedGroupId", "type": "System.Guid", "defaultValue": "", "description": "통합 그룹 만들기가 허용된 보안 그룹의 GUID."

- "name": "ClassificationList", "type": "System.String", "defaultValue": "", "description": "통합 그룹에 적용할 수 있는 유효한 분류 값을 쉼표로 구분한 목록."

- "name": "UsageGuidelinesUrl", "type": "System.String", "defaultValue": "", "description": "그룹 사용 지침 링크."

name | type | defaultValue | 설명
----------  | ----------  | ---------  | ----------
"EnableGroupCreation"  | "System.Boolean"  | "true"  | "통합 그룹 만들기 기능이 켜져 있는지 나타내는 부울 플래그."
"GroupCreationAllowedGroupId"  | "System.Guid"  | ""  | "통합 그룹 만들기가 허용 목록에 추가된 보안 그룹의 GUID."
"ClassificationList"  | "System.String"  | ""  | "통합 그룹에 적용할 수 있는 유효한 분류 값을 쉼표로 구분한 목록."
"UsageGuidelinesUrl"  | "System.String"  | ""  | "그룹 사용 지침 링크."

## <a name="next-steps"></a>다음 단계

[Azure Active Directory Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=808260)에서 더 많은 Azure Active Directory PowerShell 설명서를 찾을 수 있습니다.

[Rob의 그룹 블로그](http://robsgroupsblog.com/blog/configuring-settings-for-office-365-groups-in-azure-ad)에서 Microsoft 프로그램 관리자 Rob de Jong의 자세한 안내가 제공됩니다.

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md)

* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)



<!--HONumber=Oct16_HO2-->


