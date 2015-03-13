Azure 고객은 매달 25,000통의 무료 메일의 잠금을 해제할 수 있습니다. 매달 이러한 25,000통의 무료 메일을 통해 고급 보고 및 분석과 [모든 API][](Web, SMTP, Event, Parse, Sub-User)에 액세스할 수 있습니다. SendGrid에서 제공하는 추가 서비스에 대한 내용은 [SendGrid 기능][](영문) 페이지를 참조하세요.

### SendGrid 계정을 등록하려면

1. [Azure 관리 포털][]에 로그인합니다.

2. 관리 포털의 아래쪽 창에서 **새로 만들기**를 클릭합니다.

	![command-bar-new][command-bar-new]

3. **마켓플레이스**를 클릭합니다.

	![sendgrid-store][sendgrid-store]

4. **응용 프로그램 및 서비스 선택** 대화 상자에서 **SendGrid**를 선택하고 오른쪽 화살표를 클릭합니다.

5. **응용 프로그램 및 서비스 개인 설정** 대화 상자에서 등록할 **SendGrid** 플랜을 선택합니다.

6. Azure 설정에서 사용자의 **SendGrid** 서비스를 식별하기 위한 이름을 입력하거나 **SendGridEmailDelivery.Simplified.SMTPWebAPI** 기본값을 사용합니다. 이름은 1자에서 100자 사이의 문자여야 하며, 영숫자, 대시, 점, 밑줄만 포함됩니다. 이 이름은 가입한 Azure 저장소 항목 목록에서 고유해야 합니다.

	![store-screen-2][store-screen-2]

7. 지역 값(예: 미국 서부)을 선택합니다.

8. 오른쪽 화살표를 클릭합니다.

9. **구입 검토** 탭에서 요금제 및 가격 정보를 검토하고 약관을 확인합니다. 약관에 동의하면 확인 표시를 클릭합니다. 확인 표시를 클릭하면 SendGrid 계정의 [SendGrid 프로비전 프로세스]가 시작됩니다.

	![store-screen-3][store-screen-3]

10. 구매를 확인하면 추가 기능 대시보드로 리디렉션되고 **Purchasing SendGrid**라는 메시지가 나타납니다.

	![sendgrid-purchasing-message][sendgrid-purchasing-message]

	SendGrid 계정이 즉시 프로비전되며 **Successfully purchased Add-On SendGrid**라는 메시지가 나타납니다. 이제 사용자의 계정과 자격 증명이 만들어졌습니다. 이제 메일을 보낼 준비가 되었습니다. 

	구독 계획을 수정하거나 SendGrid 연락처 설정을 보려면 SendGrid 서비스 이름을 클릭하여 SendGrid 마켓플레이스 대시보드를 여세요. 

	![sendgrid-add-on-dashboard][sendgrid-add-on-dashboard]

	SendGrid를 사용하여 메일을 보내려면 계정 자격 증명(사용자 이름 및 암호)을 제공해야 합니다.

### SendGrid 자격 증명을 찾으려면 ###

1. **연결 정보**를 클릭합니다.

	![sendgrid-connection-info-button][sendgrid-connection-info-button]

2.  *Connection info* 대화 상자에서 이 자습서에서 나중에 사용하도록 **암호**와 사용자 이름을 복사합니다.

	![sendgrid-connection-info][sendgrid-connection-info]

	메일 배달 설정을 설정하려면 **관리** 단추를 클릭합니다. 그러면 로그인할 수 있는 Sendgrid.com 웹 인터페이스가 열리고 SendGrid 제어판이 열립니다. 

	![sendgrid-control-panel][sendgrid-control-panel]

	SendGrid 시작에 대한 자세한 내용은 [SendGrid 시작][](영문)을 참조하세요.

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[SendGrid 기능]: http://sendgrid.com/features
[Azure 관리 포털]: https://manage.windowsazure.com
[SendGrid 시작]: http://sendgrid.com/docs
[SendGrid 프로비전 프로세스]: https://support.sendgrid.com/hc/ko-kr/articles/200181628-Why-is-my-account-being-provisioned-
[모든 API]: https://sendgrid.com/docs/API_Reference/index.html

<!--HONumber=42-->
