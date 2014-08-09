<properties linkid="develop-mobile-tutorials-get-started-sencha" urlDisplayName="Get Started" pageTitle="Get Started with Azure Mobile Services and Sencha" metaKeywords="" description="Follow this tutorial to get started developing with Mobile Services and the Sencha HTML5 mobile app framework. " metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="" documentationCenter="Mobile" title="Get started with Mobile Services and Sencha Touch" authors="sencha" solutions="" manager="" editor="" />

모바일 서비스 및 Sencha Touch 시작
==================================

[Windows 스토어](/ko-kr/documentation/articles/mobile-services-windows-store-get-started "Windows 스토어") [Windows Phone](/ko-kr/documentation/articles/mobile-services-windows-phone-get-started "Windows Phone") [iOS](/ko-kr/documentation/articles/mobile-services-ios-get-started "iOS") [Android](/ko-kr/documentation/articles/mobile-services-android-get-started "Android") [HTML](/ko-kr/documentation/articles/mobile-services-html-get-started "HTML") [Xamarin.iOS](/ko-kr/documentation/articles/partner-xamarin-mobile-services-ios-get-started "Xamarin.iOS") [Xamarin.Android](/ko-kr/documentation/articles/partner-xamarin-mobile-services-android-get-started "Xamarin.Android") [Sencha](/ko-kr/documentation/articles/partner-sencha-mobile-services-get-started/ "Sencha") [PhoneGap](/ko-kr/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/ "PhoneGap")

이 자습서에서는 Sencha Touch 응용 프로그램에서 Azure 모바일 서비스를 활용하는 방법을 보여 줍니다. 관리 포털을 통해 정의하는 모바일 서비스를 활용하며 Sencha Touch를 사용하는 간단한 To Do List 앱을 만듭니다. 이 자습서는 JavaScript에 대해 잘 이해하고 있으며 Sencha Touch 프레임워크에 익숙한 중급 및 고급 웹 응용 프로그램 개발자를 대상으로 합니다.

동영상을 시청하려는 경우 오른쪽에 있는 클립은 이 자습서와 동일한 단계를 따릅니다. 비디오에서는 Arthur Kay가 Azure 모바일 서비스 백 엔드를 사용하여 Sencha Touch 응용 프로그램을 빌드하는 방법을 설명합니다.

[자습서 보기](http://go.microsoft.com/fwlink/?LinkId=392574) [동영상 재생](http://go.microsoft.com/fwlink/?LinkId=392574) 14:37

완성된 앱의 스크린샷은 다음과 같습니다.

![](./media/partner-sencha-mobile-services-get-started/finished-app.png)

요구 사항
---------

-   [Sencha Touch](http://wwww.sencha.com/products/touch/download)(영문)를 다운로드하여 설치합니다.

-   [Sencha Cmd 도구](http://www.sencha.com/products/sencha-cmd/download)(영문)를 다운로드하여 설치합니다.

-   JRE(Java Runtime Environment) 또는 Java Development Kit(Android 앱을 만드는 경우)

새 모바일 서비스 만들기
-----------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

TodoItems 테이블 만들기
-----------------------

모바일 서비스를 만들고 나면 관리 포털에서 쉬운 빠른 시작에 따라 모바일 서비스에 사용할 새 데이터베이스 테이블을 만들 수 있습니다.

1.  관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2.  빠른 시작 탭에서 **플랫폼 선택** 아래의 **HTML**을 클릭하고 **새 HTML 앱 만들기**를 확장합니다.

    ![모바일 빠른 시작 html](./media/partner-sencha-mobile-services-get-started/mobile-portal-quickstart-html.png)

    This displays the three easy steps to create and host an HTML app connected to your mobile service.

    ![모바일 빠른 시작 html](./media/partner-sencha-mobile-services-get-started/mobile-quickstart-steps-html.png)

3.  **TodoItems 테이블 만들기**를 클릭하여 앱 데이터를 저장할 테이블을 만듭니다.

    **참고**

    관리 포털에서 HTML 앱을 다운로드하지 마십시오. 대신, 아래 섹션에서 직접 Sencha Touch 응용 프로그램을 만듭니다.

4.  관리 포털에서 **appKey** 및 **appUrl**을 기록해 두십시오. 이 자습서의 다른 섹션에서 이 정보를 사용합니다.

    ![앱 키](./media/partner-sencha-mobile-services-get-started/mobile-app-key-portal.png)

5.  **구성** 탭의 **Cross-Origin Resource Sharing (CORS)** 아래에 있는 **Allow requests from host names** 목록에 `localhost`가 이미 나열되어 있는지 확인합니다. 그렇지 않으면 **호스트 이름** 필드에 `localhost`를 입력하고 **저장**을 클릭합니다.

    ![localhost의 CORS 설정](./media/partner-sencha-mobile-services-get-started/mobile-services-set-cors-localhost.png)

Touch 응용 프로그램 생성
------------------------

Sencha Touch 템플릿 응용 프로그램을 생성하는 일은 Sencha Cmd를 사용하는 간단한 작업이며 이렇게 하면 매우 신속하게 응용 프로그램을 실행할 수 있습니다.

Touch 프레임워크를 설치한 디렉터리에서 다음 명령을 실행합니다.

    $ sencha generate app Basic /path/to/application

응용 프로그램 이름이 'Basic'인 템플릿 Touch 응용 프로그램이 생성됩니다. 응용 프로그램을 시작하려면 브라우저를 /path/to/application 디렉터리로 가리키면 됩니다. 그러면 표준 Touch 샘플 응용 프로그램이 표시됩니다.

Azure용 Sencha Touch 확장 프로그램 설치
---------------------------------------

Azure용 확장 프로그램은 수동으로 또는 Sencha 패키지로 설치됩니다. 원하는 방법을 사용하면 됩니다.

### 수동 설치

대부분의 Touch 응용 프로그램에서는 외부 클래스 라이브러리를 추가하려는 경우 패키지를 다운로드하고 응용 프로그램 디렉터리에 압축을 푼 다음 해당 라이브러리 위치로 Touch 로더를 구성하면 됩니다.

다음 단계를 사용하여 응용 프로그램에 Azure 확장 프로그램을 수동으로 추가할 수 있습니다.

1.  Azure 확장 패키지는 [여기](https://market.sencha.com/extensions/sencha-touch-extensions-for-windows-azure)(영문)에서 다운로드합니다. 이 영역에 액세스하려면 Sencha Forums ID를 사용할 수 있습니다.

2.  다운로드 디렉터리에서 Azure 확장 패키지를 복사하여 최종적으로 배치하여 압축을 풀 위치에 넣습니다.

         $ cd /path/to/application
         $ mv /download-location/azure.zip .
	     $ unzip azure.zip

    패키지 원본, 예제 및 설명서가 포함된 **azure** 디렉터리가 만들어집니다. 원본은 **azure/src** 디렉터리에 위치합니다.

### Sencha 패키지로 설치

**참고**

`sencha generate app` 명령을 사용하여 응용 프로그램을 생성한 경우에만 이 방법을 사용할 수 있습니다.

Sencha Cmd로 생성된 모든 응용 프로그램에는 루트에 "packages" 폴더가 있습니다. 이 폴더의 위치는 구성할 수 있지만, 위치에 상관없이 "packages" 폴더의 역할은 응용 프로그램에서 사용되는 모든 패키지(또는 Sencha Workspace를 만든 경우 여러 응용 프로그램)의 저장소 역할입니다.

Ext.Azure는 Sencha Cmd "패키지"이므로, Sencha Cmd를 사용하여 원본 코드를 쉽게 설치하고 응용 프로그램에 포함할 수 있습니다. 자세한 내용은 [Sencha Cmd 패키지](http://docs.sencha.com/cmd/3.1.2/#!/guide/command_packages)(영문)를 참조하십시오.

Sencha 패키지 리포지토리에서 Azure 확장 패키지를 다운로드하여 설치하려면 **app.json** 파일에 패키지 이름을 추가하고 응용 프로그램을 빌드해야 합니다.

1.  app.json 파일의 requires 섹션에 Azure 패키지를 추가합니다.

		{
			"name": "Basic",
            "requires": [
		 		"touch-azure"
            ]
		}

2.  패키지를 가져오고 설치하는 **sencha cmd**를 사용하여 응용 프로그램을 다시 빌드합니다.

         $ sencha app build

**sencha app build**와 **sencha app refresh**는 모두 응용 프로그램에 패키지를 통합하는 데 필요한 단계를 수행합니다. 일반적으로 패키지 요구 사항을 변경한 후에는 "dev mode"를 지원하는 데 필요한 메타데이터가 최신이 되도록 **sencha app refresh**를 실행해야 합니다.

어느 명령을 실행하든, Sencha Cmd는 "packages" 폴더에 패키지를 다운로드하고 압축을 풉니다. 이렇게 하고 나면 작업 영역에 "packages/touch-azure" 폴더가 나타납니다.

Azure 포함 및 구성
------------------

**Filename**: app.js

이제 Azure 확장 프로그램을 다운로드하여 응용 프로그램 디렉터리에 설치했습니다. 다음 단계는 원본 파일을 찾을 위치를 응용 프로그램에 지정하고 해당 파일을 요청하는 것입니다.

1.  원본 코드의 위치를 사용하여 Sencha 로더를 구성합니다.

         Ext.Loader.setConfig({
                enabled : true,
                paths   : {
                    'Ext'       : 'touch/src',
                    'Ext.azure' : '/path-to/azure-for-touch/azure/src'
             }
         });

2.  Azure 클래스 파일을 요청합니다.

         Ext.application({

             requires: [ 'Ext.azure.Azure' ],

             // ...

         });

3.  Azure 구성

    Azure 패키지는 응용 프로그램의 launch 섹션에서 **Ext.Azure.init** 메서드를 호출하여 초기화합니다. 이 메서드는 모바일 서비스 자격 증명 및 활용하려는 기타 자격 증명과 기능이 포함된 구성 개체에 전달됩니다.

    구성 개체를 init 메서드에 바로 전달할 수도 있지만 **azure**라는 Sencha 응용 프로그램 구성 속성을 만들어 여기에 모든 해당 정보를 넣는 것이 좋습니다. 그런 다음 이 속성 값을 Ext.Azure.init 메서드에 전달할 수 있습니다.

    Azure에서 모바일 서비스를 만들면([Azure 시작](http://senchaazuredocs.azurewebsites.net/#!/guide/getting_started)(영문) 참조) 이 서비스에 응용 프로그램 키 및 URL이 할당됩니다. 이 정보를 Azure 패키지에 제공해야 패키지가 서비스에 연결할 수 있습니다.

    다음 예는 응용 프로그램 키와 URL만 제공하는 매우 간단한 Azure 구성 및 초기화를 보여 줍니다.

		Ext.application({
	    	name: 'Basic',

            requires: [ 'Ext.azure.Azure' ],

            azure: {
			    appKey: 'myazureservice-access-key',
                appUrl: 'myazure-service.azure-mobile.net'
            },

    		launch: function() {

                 // Call Azure initialization

                Ext.Azure.init(this.config.azure);

			}
		});

    Azure 구성 옵션에 대한 자세한 내용은 Ext.Azure API 설명서를 참조하십시오.

축하합니다. 이제 응용 프로그램이 모바일 서비스에 액세스할 수 있게 되었습니다.

ToDo 앱 빌드
------------

지금까지 Azure 확장 프로그램을 포함하도록 응용 프로그램을 구성하고 응용 프로그램에 모바일 서비스 자격 증명을 제공했습니다. 이제 서비스에 저장된 ToDo 목록 데이터를 보고 편집하는 데 모바일 서비스를 활용하는 Touch 응용 프로그램을 만드는 과정으로 진행할 수 있습니다.

### Azure 데이터 프록시 구성

**Filename:** app/model/TodoItem.js

Touch 응용 프로그램은 데이터 프록시를 통해 모바일 서비스와 통신합니다. 프록시는 모바일 서비스에 요청을 보내고 모바일 서비스로부터 데이터를 수신하는 작업을 모두 수행합니다. Touch 데이터 모델 및 저장소와 함께 사용하면 원격 데이터를 처리하여 응용 프로그램에 포함하는 모든 힘든 작업을 직접 수행할 필요 없이 Touch 자체에서 처리합니다.

Sencha Touch 모델은 응용 프로그램에서 사용할 데이터 레코드의 정의를 제공합니다. 이 모델에서는 데이터 필드를 정의할 뿐만 아니라 응용 프로그램과 Azure 모바일 서비스 사이의 통신을 처리할 프록시에 대한 구성도 제공할 수 있습니다.

다음 코드에서는 모델의 필드(및 해당 형식)를 정의하고 프록시 구성을 제공합니다. 프록시를 구성할 때는 해당 유형(이 경우 'azure'), 모바일 서비스 테이블 이름(ToDoItem), 기타 선택적인 매개 변수를 지정해야 합니다. 이 예에서는 목록 항목을 원활하게 페이지 앞으로 및 뒤로 이동할 수 있도록 프록시 페이징을 설정합니다.

Azure 프록시는 Azure API에서 요구되는 적합한 CRUD 작업을 사용하여(있는 경우 인증 자격 증명 포함) 모든 HTTP 헤더를 자동 설정합니다.

    Ext.define('Basic.model.TodoItem', {
        extend : 'Ext.data.Model',

        requires : [
            'Ext.azure.Proxy'
        ],

        config : {
            idProperty : 'id',
            useCache   : false,

            fields     : [
                {
                    name : 'id',
                    type : 'int'
                },
                {
                    name : 'text',
                    type : 'string'
                },
                {
                    name : 'complete',
                    type : 'boolean'
                }
            ],

            proxy : {
                type               : 'azure',
                tableName          : 'TodoItem',
                enablePagingParams : true
            }
        }
    });

### ToDo 항목 저장

**Filename**: app/store/TodoItems.js

Sencha Touch 저장소는 다양한 방식으로 레코드를 표시할 수 있도록 Touch 구성 요소의 원본으로 사용할 수 있는 데이터 레코드(모델) 컬렉션을 저장하는 데 사용됩니다. 여기에는 표, 차트, 목록 등이 포함될 수 있습니다.

여기서는 Azure 모바일 서비스에서 검색되는 모든 저장소 ToDo 목록 항목을 보관하는 데 사용되는 저장소를 정의합니다. 저장소 구성에는 모델 유형(Basic.model.TodoItem - 위에서 정의됨)의 이름이 포함됩니다. 여기서는 저장소에 포함될 레코드의 구조가 정의됩니다.

예를 들어 페이지 크기(8개 레코드)를 지정하는 등, 저장소에 사용할 몇 가지 추가 구성 옵션도 있으며 이 저장소 레코드 정렬은 Azure 모바일 서비스에서 원격으로 이루어집니다(정렬은 저장소 자체 내에서 로컬로 수행되지 않음).

    Ext.define('Basic.store.TodoItems', {
        extend : 'Ext.data.Store',

        requires : [
            'Basic.model.TodoItem'
        ],

        config : {
            model        : 'Basic.model.TodoItem',
            pageSize     : 8,
            remoteSort   : true,
            remoteFilter : true
        }
    });

### ToDo 항목 보기 및 편집

**Filename**: app/view/DataItem.js

지금까지 각 ToDo 항목의 구조를 정의하고 모든 레코드를 배치할 저장소를 만들었습니다. 이제 이 정보를 앱 사용자에게 표시하는 방법에 대해 생각해야 합니다. 일반적으로 **뷰**를 사용하여 정보를 사용자에게 표시합니다. 뷰는 개별 구성 요소나 결합된 구성 요소에 대해 Touch 구성 요소 수만큼 사용할 수 있습니다.

다음 뷰는 각 항목을 삭제하는 동작을 수용하는 일부 단추와 함께 각 레코드가 표시되는 방식을 정의하는 ListItem으로 구성됩니다.

    Ext.define('Basic.view.DataItem', {
        extend : 'Ext.dataview.component.ListItem',
        xtype  : 'basic-dataitem',

        requires : [
            'Ext.Button',
            'Ext.layout.HBox',
            'Ext.field.Checkbox'
        ],

        config : {
            checkbox : {
                docked     : 'left',
                xtype      : 'checkboxfield',
                width      : 50,
                labelWidth : 0
            },

            text : {
                flex : 1
            },

            button : {
                docked   : 'right',
                xtype    : 'button',
                ui       : 'plain',
                iconMask : true,
                iconCls  : 'delete',
                style    : 'color: red;'
            },

            dataMap : {
                getText : {
                    setHtml : 'text'
                },

                getCheckbox : {
                    setChecked : 'complete'
                }
            },

            layout : {
                type: 'hbox',
                align: 'stretch'
            }
        },

        applyCheckbox : function(config) {
            return Ext.factory(config, Ext.field.Checkbox, this.getCheckbox());
        },

        updateCheckbox : function (cmp) {
            if (cmp) {
                this.add(cmp);
            }
        },

        applyButton : function(config) {
            return Ext.factory(config, Ext.Button, this.getButton());
        },

        updateButton : function (cmp) {
            if (cmp) {
                this.add(cmp);
            }
        }

    });

### 기본 뷰 만들기

**Filename**: app/view/Main.js

지금까지 개별 ToDo 목록 항목의 레이아웃을 정의했습니다(위). 이제 실제 항목 목록, 응용 프로그램 제목, 새 작업을 추가하는 단추 등을 정의하는 전체 사용자 인터페이스를 이 목록 주위에 래핑하겠습니다.

    Ext.define('Basic.view.Main', {
        extend : 'Ext.dataview.List',
        xtype  : 'main',

        requires : [
            'Ext.TitleBar',
            'Ext.dataview.List',
            'Ext.data.Store',
            'Ext.plugin.PullRefresh',
            'Ext.plugin.ListPaging',
            'Basic.view.DataItem'
        ],

        config : {
            store : 'TodoItems',

            useSimpleItems : false,
            defaultType    : 'basic-dataitem',

            plugins : [
                {
                    xclass          : 'Ext.plugin.PullRefresh',
                    pullRefreshText : 'Pull down to refresh!'
                },
                {
                    xclass     : 'Ext.plugin.ListPaging',
                    autoPaging : true
                }
            ],

            scrollable : {
                direction     : 'vertical',
                directionLock : true
            },

            items : [
                {
                    docked : 'top',
                    xtype  : 'titlebar',
                    title  : 'Azure Mobile - Basic Data Example'
                },
                {
                    xtype  : 'toolbar',
                    docked : 'bottom',
                    items  : [
                        {
                            xtype       : 'textfield',
                            placeHolder : 'Enter new task',
                            flex        : 1
                        },
                        {
                            xtype  : 'button',
                            action : 'add',
                            text   : 'Add'
                        }
                    ]
                }
            ]
        }
    });

### 모든 요소가 함께 작동하도록 만들기

**Filename**: app/controller/Main.js

응용 프로그램에서 마지막 단계는 단추 누름(삭제, 저장 등)에 응답하고 이 모든 요청 이면에 논리를 제공하는 것입니다. Sencha Touch는 이런 이벤트를 수신 대기하고 그에 따라 응답하는 컨트롤러를 활용합니다.

    Ext.define('Basic.controller.Main', {
        extend : 'Ext.app.Controller',

        config : {
            refs : {
                todoField : 'main toolbar textfield',
                main      : 'main'
            },

            control : {
                'button[action=add]'    : {
                    tap : 'onAddItem'
                },
                'button[action=reload]' : {
                    tap : 'onReload'
                },

                main : {
                    activate      : 'loadInitialData',
                    itemdoubletap : 'onItemEdit'
                },

                'basic-dataitem checkboxfield' : {
                    change : 'onItemCompleteTap'
                },

                'basic-dataitem button' : {
                    tap : 'onItemDeleteTap'
                }
            }
        },

        loadInitialData : function () {
            Ext.getStore('TodoItems').load();
        },

        onItemDeleteTap : function (button, e, eOpts) {
            var store    = Ext.getStore('TodoItems'),
                dataItem = button.up('dataitem'),
                rec      = dataItem.getRecord();

            rec.erase({
                success: function (rec, operation) {
                    store.remove(rec);
                },
                failure: function (rec, operation) {
                    Ext.Msg.alert(
                        'Error',
                        Ext.util.Format.format('There was an error deleting this task.<br/><br/>    Status Code: {0}<br/>Status Text: {1}', 
                        operation.error.status, 
                        operation.error.statusText)
                    );
                }
            });
        },

        onItemCompleteTap : function (checkbox, newVal, oldVal, eOpts) {
            var dataItem = checkbox.up('dataitem'),
                rec      = dataItem.getRecord(),
                recVal   = rec.get('complete');

            // this check is needed to prevent an issue where multiple creates get triggered from one create
            if (newVal !== recVal) {
                rec.set('complete', newVal);
                rec.save({
                    success: function (rec, operation) {
                        rec.commit();
                    },
                    failure: function (rec, operation) {
                        // since there was a failure doing the update on the server then silently reject the change
                        rec.reject(true);
                        Ext.Msg.alert(
                            'Error',
                            Ext.util.Format.format('There was an error updating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}', 
                            operation.error.status, 
                            operation.error.statusText)
                        );
                    }
                });
            }
        },

        onItemEdit : function (list, index, target, record, e, eOpts) {
            var rec = list.getSelection()[0];

            Ext.Msg.prompt('Edit', 'Rename task',
                function (buttonId, value) {
                    if (buttonId === 'ok') {
                        rec.set('text', value);
                        rec.save({
                            success: function (rec, operation) {
                                rec.commit();
                            },
                            failure: function (rec, operation) {
                                // since there was a failure doing the update on the server then reject the change
                                rec.reject();
                                Ext.Msg.alert(
                                    'Error',
                                    Ext.util.Format.format('There was an error updating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}', 
                                    operation.error.status, 
                                    operation.error.statusText)
                                );
                            }
                        });
                    }
                },
                null,
                false,
                record.get('text')
            );
        },

        onReload : function () {
            Ext.getStore('TodoItems').load();
        },

        onAddItem : function () {
            var me = this,
                rec,
                store = Ext.getStore('TodoItems'),
                field = me.getTodoField(),
                value = field.getValue();

            if (value === '') {
                Ext.Msg.alert('Error', 'Please enter Task name', Ext.emptyFn);
            }
            else {
                rec = Ext.create('Basic.model.TodoItem', {
                    complete : false,
                    text     : value
                });
                //store.insert(0, rec); //insert at the top
                //store.sync();
                rec.save({
                    success: function (rec, operation) {
                        store.insert(0, rec); //insert at the top
                        field.setValue('');
                    },
                    failure: function (rec, operation) {
                        Ext.Msg.alert(
                            'Error',
                            Ext.util.Format.format('There was an error creating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}', 
                            operation.error.status, 
                            operation.error.statusText)
                        );
                    }
                });
            }
        }
    });

### 모든 요소 결합

**Filename**: app.js

마지막 단계는 기본 응용 프로그램 파일 편집을 마치고 정의된 모델, 저장소, 뷰, 컨트롤러에 대한 정보를 제공하는 것입니다. 이 리소스의 원본 파일은 자동으로 응용 프로그램에 로드됩니다. 마지막으로, 기본 응용 프로그램 뷰인 'Basic.main.View'를 만들고 표시하는 launch 메서드를 호출합니다.

    Ext.Loader.setConfig({
        enabled : true,
        paths   : {
            'Ext'       : 'touch/src',
            'Ext.azure' : 'packages/azure/src'
        }
    });

    Ext.application({
        name : 'Basic',

        requires : [
            'Ext.MessageBox',
            'Ext.azure.Azure'
        ],

        views : [
            'Main'
        ],

        controllers : [
            'Main'
        ],

        stores : [
            'TodoItems'
        ],

        azure : {
            appUrl : 'YOUR_APP_URL.azure-mobile.net',
            appKey : 'YOUR_APP_KEY'
        },

        icon : {
            '57'  : 'resources/icons/Icon.png',
            '72'  : 'resources/icons/Icon~ipad.png',
            '114' : 'resources/icons/Icon@2x.png',
            '144' : 'resources/icons/Icon~ipad@2x.png'
        },

        isIconPrecomposed : true,

        startupImage : {
            '320x460'   : 'resources/startup/320x460.jpg',
            '640x920'   : 'resources/startup/640x920.png',
            '768x1004'  : 'resources/startup/768x1004.png',
            '748x1024'  : 'resources/startup/748x1024.png',
            '1536x2008' : 'resources/startup/1536x2008.png',
            '1496x2048' : 'resources/startup/1496x2048.png'
        },

        launch : function () {
            // Destroy the #appLoadingIndicator element
            Ext.fly('appLoadingIndicator').destroy();

            // Initialize Azure
            Ext.Azure.init(this.config.azure);

            // Initialize the main view
            Ext.Viewport.add(Ext.create('Basic.view.Main'));
        },

        onUpdated : function () {
            Ext.Msg.confirm(
                "Application Update",
                "This application has just successfully been updated to the latest version. Reload now
        ",
                function (buttonId) {
                    if (buttonId === 'yes') {
                        window.location.reload();
                    }
                }
            );
		}
    });

### Sencha Touch 앱 호스트 및 실행

이 자습서의 마지막 단계는 새로운 앱을 로컬 컴퓨터에 호스트하고 실행하는 것입니다.

  1.  터미널에서 압축이 풀린 응용 프로그램의 위치로 이동합니다.

  2.  Sencha Cmd를 사용하여 다음 명령을 실행합니다.

    * *sencha app refresh* : Sencha Cmd에 모든 앱 종속성을 찾아
      필요한 패키지를 다운로드하도록 지시합니다(예: [Azure용 Sencha Touch 확장 프로그램](https://market.sencha.com/extensions/sencha-touch-extensions-for-windows-azure)).

    * *sencha web start* : 응용 프로그램을 테스트하는 로컬 웹 서버를 시작합니다.

    ![sencha web start](./media/partner-sencha-mobile-services-get-started/sencha-web-start.png)

  3.  터미널에서 나열된 URL(예: http://localhost:1841)을 웹 브라우저에서 열고 앱을 시작합니다.

  4.  앱에서 "Complete the tutorial" 등의 의미 있는 텍스트를 입력하고 **Add**를 클릭합니다.


    ![new todo item](./media/partner-sencha-mobile-services-get-started/new-todo-item.png)

    Azure에 호스트된 새 모바일 서비스에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다.

  5.  관리 포털로 돌아가서 **데이터** 탭을 클릭한 후 TodoItems 테이블을 클릭합니다.

    ![Todo Items table](./media/partner-sencha-mobile-services-get-started/mobile-data-tab.png)

    이를 통해 앱에서 테이블로 삽입된 데이터를 찾을 수 있습니다.

    ![browse todo table](./media/partner-sencha-mobile-services-get-started/mobile-data-browse.png)

다음 단계
---------

이제 시작하기 가이드를 완료했으며 Sencha를 사용하여 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다.

추가 스타일 및 기능이 있는 완성된 샘플 앱을 [다운로드](https://github.com/arthurakay/sencha-touch-azure-example)하여 Sencha Touch에서 제공되는 다른 이점을 확인하십시오.

그런 다음, Azure용 Sencha Touch 확장 프로그램에 대한 자세한 내용을 알아보십시오.

-   샘플 앱 [연습](http://docs.sencha.com/touch-azure/1.0.0/#!/guide/data_filters)(영문)
-   [Sencha Forums](http://www.sencha.com/forum)(영문)에서 도움말 얻기
-   [Sencha 설명서](http://docs.sencha.com/)(영문) 찾아보기
-   Azure 모바일 서비스에서 Sencha 사용: [(비디오)(영문)](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-126-Using-Sencha-With-Windows-Azure-Mobile-Services)

추가 리소스
-----------

-   [Sencha Touch 다운로드(영문)](http://pages.sencha.com/touch-for-azure.html)
-   [Azure용 Sencha Touch 확장 프로그램(영문)](https://market.sencha.com/extensions/sencha-touch-extensions-for-windows-azure)

요약
----

여기에 나온 예는 Azure용 Sencha Touch 확장 프로그램 패키지로 제공되며 Basic Data 예로 예제 디렉터리에 있습니다. 이 확장 프로그램의 다른 기능을 시연하는 예가 몇 가지 더 있으며 이와 함께 자세한 의견과 설명이 제공됩니다.

Sencha Touch 시작에 대한 자세한 내용을 보려면 전체 [가이드](http://docs.sencha.com/touch/#!/guide)(영문)를 방문하십시오.

<!-- images -->
[0]: ./media/partner-sencha-mobile-services-get-started/finished-app.png
