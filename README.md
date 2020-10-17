# 개요

타일즈는 화면마다 반복 사용되는 헤더, 푸터같은 요소들을 반복작성하게 되는 일을 줄여주는 화면 관련 프레임워크이다.

화면 내 요소들을 화장실 타일처럼 구분하고 타일 끼어넣듯 사용한다고 하여 이름이 타일즈임.

베이스가 되는 뷰페이지 하나를 만든뒤 바꿔끼고 싶은 곳에 타일즈 태그를 작성하면 컨트롤러가 리턴한 뷰페이지가 해당 타일즈 태그 작성위치 자리잡아 표현됨.

요약하면 하나의 베이스 파일에 타일즈태그위치(내용)만 바꿔 끼어가며 표현함으로써 공통 레이아웃에 대한 반복작성을 줄여줌.



# 절차

1. 라이브러리 추가

2. layout.jsp 작성 

3. tiles-def.xml 작성

4. servlet-context.xml에 tilesViewResolver, tilesConfigurer 작성

   

# 라이브러리 추가

![](C:\choimory Files\others\capture\Tiles 3 사용하기\2020-10-17 23 51 22.png)

- tiles-core
- tiles-jsp
  - JSP에서 타일즈 태그를 사용할 수 있게 해주는 taglib을 명시하려면 tiles-jsp 라이브러리도 추가되어야함 
  - Tiles도 JSTL, EL과 똑같이 커스텀태그+@로 만들어져 있음. 단 위의 것들은 스프링에 기본적으로 포함된 라이브러리라서 별도의 라이브러리 추가 없이도 바로 사용이 가능한것.

> Tiles 3는 Spring 4부터 가능

# 레이아웃 템플릿 JSP파일 작성

![](C:\choimory Files\others\capture\Tiles 3 사용하기\2020-10-17 23 51 33.png)

## taglib 선언

`<%@ taglib prefix="tiles" uri="http://tiles.apache.org/tags-tiles" %>`

## 타일즈태그 사용

하나의 JSP 파일에 공통요소들을 작성해놓은뒤, 파일에 따라 변경되는 부분만 `<tiles:insertAttribute name="">`태그를 사용하여 변경하게 함

주로 CDN/헤더/컨테이너(타일즈)/푸터의 구성

리턴하는 뷰페이지가 하나이므로 타일즈 태그는 한곳만 사용 가능



# 타일즈 설정 XML파일 작성

![](C:\choimory Files\others\capture\Tiles 3 사용하기\2020-10-17 23 51 51.png)

## DTD

- Location : `http://tiles.apache.org/dtds/tiles-config_3_0.dtd`
- Key ID : `-//Apache Software Foundation//DTD Tiles Configuration 3.0//EN`

## 내용

- 생성한 템플릿 파일에 대한 태그를 하나 작성

  - ```xml
    <definition name="layout" template="/WEB-INF/views/layout.jsp">
    	<put-attribute name="content" value=""/>
    </definition>
    ```

- 뷰페이지 경로에 따라 연결시킬 태그들을 작성

  - ```xml
    ![2020-10-17 23 52 08](C:\choimory Files\others\capture\Tiles 3 사용하기\2020-10-17 23 52 08.png)<definition name="*.tiles" extends="layout">
    	<put-attribute name="content" value="/WEB-INF/views/{1}.jsp"/>
    </definition>
    <definition name="*/*.tiles" extends="layout">
    	<put-attribute name="content" value="/WEB-INF/views/{1}/{2}.jsp"/>
    </definition>
    ```

    

# 서블릿 설정   

![](C:\choimory Files\others\capture\Tiles 3 사용하기\2020-10-17 23 52 04.png)

UrlBasedViewResolver와 TilesConfigurer의 빈을 생성하고 설정해준다. 이때 설정값에 Tiles2, Tiles3가 들어가는 부분에 타일즈 버전을 잘못 입력하지 않도록 주의

- UrlBasedViewResolver

  - ```xml
    <beans:bean class="org.springframework.web.servlet.view.UrlBasedViewResolver">
      		<beans:property name="viewClass" value="org.springframework.web.servlet.view.tiles3.TilesView"/>
      		<beans:property name="order" value="1"/>
      	</beans:bean>
    ```

    여기서 order는 다른 ViewResolver들 사이에 먼저 적용될 우선순위임. 타일즈뷰리졸버>JSP뷰리졸버가 되도록 order값을 설정함.

- TilesConfigurer

  - ```xml
    <beans:bean class="org.springframework.web.servlet.view.tiles3.TilesConfigurer">
      		<beans:property name="definitions" value="/WEB-INF/tiles/tiles-definition.xml"/>
      	</beans:bean>	
    ```

# 사용

타일즈 설정 파일에서 설정한 이름대로 뷰를 반환하면 사용이 가능하게 됨

![](C:\choimory Files\others\capture\Tiles 3 사용하기\2020-10-17 23 52 08.png)

