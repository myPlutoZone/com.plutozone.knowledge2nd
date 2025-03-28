# com.plutozone.knowledge.development.Eclispe


## Contents
- Configuration
- Plug-in(Help > Install New Software)
- Tips


## Configuration
- When an error called "Downloading external resources is disabled." occurs(Window > Preference > Maven)
```
# Tick off the option "download artifact javadoc"
```

- Workspace settings when starting Eclipse
```
# 하기 파일에서 RECENT_WORKSPACES 값을 삭제 또는 변경
C:\> notepad.exe %ECLIPSE%\configuration\.settings\org.eclipse.ui.ide.prefs
```

- Code Templates at Eclipse(Window > References > Java > Code Style > Code Templates > Code > New Java files)
```java
/**
 * YOU ARE STRICTLY PROHIBITED TO COPY, DISCLOSE, DISTRIBUTE, MODIFY OR USE THIS PROGRAM
 * IN PART OR AS A WHOLE WITHOUT THE PRIOR WRITTEN CONSENT OF PLUTOZONE.COM.
 * PLUTOZONE.COM OWNS THE INTELLECTUAL PROPERTY RIGHTS IN AND TO THIS PROGRAM.
 * COPYRIGHT (C) ${year} PLUTOZONE.COM ALL RIGHTS RESERVED.
 *
 * 하기 프로그램에 대한 저작권을 포함한 지적재산권은 plutozone.com에 있으며,
 * plutozone.com이 명시적으로 허용하지 않는 사용, 복사, 변경 및 제 3자에 의한 공개, 배포는 엄격히 금지되며
 * plutozone.com의 지적재산권 침해에 해당된다.
 * Copyright (C) ${year} plutozone.com All Rights Reserved.
 *
 *
 * Program		: com.plutozone.knowledge
 * Description	:
 * Environment	: JRE 1.7 or more
 * File			: ${file_name}
 * Notes		:
 * History		: [NO][Programmer][Description]
 *				: [${currentDateTime:date('yyyyMMddHHmmss')}][pluto#brightsoft.co.kr][CREATE: Initial Release]
 */
${package_declaration}

/**
 * @version 1.0.0
 * @author pluto#brightsoft.co.kr
 * 
 * @since ${currentDate:date('yyyy-MM-dd')}
 * <p>DESCRIPTION:</p>
 * <p>IMPORTANT:</p>
 */
${typecomment}
${type_declaration}
```


## Plug-in(Help > Install New Software)
- Properties Editor(http://propedit.sourceforge.jp/eclipse/updates)
```
# 하기와 같이 수동으로 플러그인 설치 가능
C:> copy jp.gr.java_conf.ussiy.app.propedit_6.0.5.jar %ECLIPSE%\plugins
C:> notepad.exe %ECLIPSE%\configuration\org.eclipse.equinox.simpleconfigurator\bundles.info
...
# 하기 라인을 추가
jp.gr.java_conf.ussiy.app.propedit,6.0.5,plugins/jp.gr.java_conf.ussiy.app.propedit_6.0.5.jar,4,false
...
```


## Tips
- When a Git password Error occurs
```
Delete ID/Value Informations at References > General > Security > Secure Storage > Contents > GIT
or
C:\del C:\Users\%USER%\.eclipse\org.eclipse.equinox.security\secure_storage
```

- Remove Launch History
```
# 하기 경로에서 파일을 삭제
%WORKSPACE%/.metadata/.plugins/org.eclipse.debug.core/.launches
```
