# AsciiDoc Project

AsciiDoc으로 작성한 문서를 HTML과 PDF로 생성하는 템플릿 프로젝트입니다.
문서 작성 규칙과 요소별 예제는 생성된 문서의 "작성 가이드" 장에 있습니다.

* Asciidoctor Gradle Plugin 사용자 가이드: https://asciidoctor.github.io/asciidoctor-gradle-plugin/master/user-guide/
* Asciidoctor PDF 테마 가이드: https://docs.asciidoctor.org/pdf-converter/latest/theme/
* AsciiDoc 문법 빠른 참조: https://docs.asciidoctor.org/asciidoc/latest/syntax-quick-reference/

## 요구 사항

* JDK 17 이상 (JDK 21 권장)
* Gradle은 별도로 설치할 필요 없음 (Gradle Wrapper 포함)

## 구성 요소와 버전 관리

| 구성 요소 | 버전을 바꾸는 곳 |
|---|---|
| Gradle (Wrapper) | `./gradlew wrapper --gradle-version <버전> --gradle-distribution-sha256-sum <SHA-256>` |
| Asciidoctor Gradle Plugin | `build.gradle` 의 `plugins` 블록 |
| AsciidoctorJ, AsciidoctorJ PDF | `build.gradle` 의 `asciidoctorjVersion`, `asciidoctorjPdfVersion` |

현재 버전은 생성된 문서의 "전제 조건" 절에 표시됩니다.
`build.gradle` 이 버전 값을 문서 속성(`gradle-version`, `asciidoctorj-pdf-version` 등)으로 넘기므로 문서 본문은 따로 고칠 필요가 없습니다.

Gradle Wrapper 는 `gradle-wrapper.properties` 의 `distributionSha256Sum` 으로 내려받은 배포판을 검증합니다.
SHA-256 값은 https://gradle.org/release-checksums/ 에서 확인하십시오.

Dependabot(`.github/dependabot.yml`)이 Gradle 플러그인과 GitHub Actions 의 새 버전을 매주 확인해 PR 을 만듭니다.

## 디렉터리 구조

```
.
├── .github/
│   ├── workflows/docs.yml      # push, PR 마다 문서를 빌드하고 ZIP 을 결과물로 올리는 CI
│   └── dependabot.yml          # 의존성 버전 자동 업데이트
├── .gitattributes              # 줄바꿈(gradlew 는 LF), 바이너리 파일 지정
├── build.gradle                # Asciidoctor 설정, 공통 문서 속성, 배포 ZIP 태스크
├── gradle.properties           # 문서 버전(version)과 Gradle 실행 옵션
├── gradlew, gradlew.bat        # Gradle Wrapper 실행 스크립트
├── gradle/wrapper/             # Gradle Wrapper 설정 (버전, 체크섬)
└── src/docs/
    ├── asciidoc/
    │   ├── _attributes-ko.adoc # 모든 문서가 include 하는 공통 헤더 속성(목차, 섹션 번호, 한국어 캡션)
    │   ├── index.adoc          # 템플릿 사용 가이드. 문서 헤더(속성)와 장 include 목록
    │   ├── sample-manual.adoc  # 제품 매뉴얼 샘플(가상 제품). 9개 부(part)와 부록 include 목록
    │   ├── k3stui-manual.adoc  # 도구 매뉴얼 샘플(k3stui). 부 없이 장과 부록으로 구성
    │   ├── chapters/           # 장(chapter)별 파일. 2단계 제목(==)으로 시작
    │   │   ├── introduction.adoc
    │   │   ├── getting-started.adoc
    │   │   ├── writing-guide.adoc
    │   │   └── appendix.adoc
    │   ├── manual/             # sample-manual 의 장 파일. 부별 디렉터리(intro, install, kb, ...)로 나눔
    │   ├── k3stui/             # k3stui-manual 의 장 파일
    │   ├── images/             # 그림. 문서에서는 파일 이름만 적음 (:imagesdir: images). 문서별 하위 디렉터리 가능 (images/k3stui/)
    │   ├── examples/           # 코드 블록에 include 할 설정/소스 예제
    │   └── docinfo.html        # HTML 출력에만 덧붙는 스타일(한국어 글꼴)
    └── theme/
        ├── DataDynamics-theme.yml    # 기본 PDF 테마. 기본 테마를 extends 하고 바꾸는 항목만 정의
        ├── KaiGenGothicKR-theme.yml  # 대체 PDF 테마. 기본 테마를 extends 하는 단순한 회색 테마
        └── *.ttf                     # PDF 에 내장할 글꼴 (아래 "글꼴" 참고)
```

## 문서 생성

```
./gradlew build
```

생성 결과는 다음 위치에 만들어집니다.

* HTML: `build/docs/asciidoc/` 아래 `index.html`, `sample-manual.html`, `k3stui-manual.html`
* PDF: `build/docs/asciidocPdf/` 아래 `index.pdf`, `sample-manual.pdf`, `k3stui-manual.pdf`
* 배포 ZIP: `build/distributions/<프로젝트명>-<버전>.zip`

배포 ZIP 의 구성은 다음과 같습니다.

```
reference/
├── pdf/
│   ├── <프로젝트명>-reference.pdf
│   ├── sample-manual.pdf
│   └── k3stui-manual.pdf
└── htmlsingle/
    ├── <프로젝트명>-reference.html
    ├── sample-manual.html
    ├── k3stui-manual.html
    └── images/
```

HTML 또는 PDF만 따로 생성하려면 `./gradlew asciidoctor` 또는 `./gradlew asciidoctorPdf`를 사용하십시오.

만약에 Gradle 캐쉬로 문서 생성이 잘 되지 않는다면 다음과 같이 빌드 결과를 삭제한 후 다시 생성하십시오.

```
./gradlew clean build
```

> **configuration cache 는 켜지 마십시오.** Asciidoctor Gradle Plugin 4.0.5 가 configuration cache 를 지원하지 않아
> `--configuration-cache` 옵션이나 `org.gradle.configuration-cache=true` 설정을 쓰면 빌드가 실패합니다.

## 버전과 날짜

문서 표지의 버전은 `gradle.properties` 의 `version` 값이고, 날짜는 빌드 시점의 날짜입니다.
`build.gradle` 이 `revnumber`, `revdate` 속성으로 전달하므로 `index.adoc` 의 값을 직접 고칠 필요가 없습니다.

### 문서 버전을 올릴 때

1. `gradle.properties` 의 `version` 을 올립니다.
2. `src/docs/asciidoc/chapters/appendix.adoc` 의 "변경 이력" 표 맨 아래에 새 버전, 날짜, 변경 내용을 한 줄 추가합니다.
   변경 이력은 문서 부록에 함께 실리므로 별도 CHANGELOG 파일은 두지 않습니다.
3. 필요하면 `index.adoc` 의 `revremark`(예: 초안, 검토본, 확정본)를 바꿉니다.

## 매뉴얼 샘플

구성 방식이 다른 두 가지 매뉴얼 샘플이 있습니다.

| 문서 | 구성 | 내용 |
|---|---|---|
| `sample-manual.adoc` | 부(part) 9개 + 장 + 부록 | 가상의 제품 "Lumen 지식 검색". 제품명, 화면, 설정, 수치는 모두 예시입니다 |
| `k3stui-manual.adoc` | 장 + 부록 (부 없음) | 공개 프로젝트 [k3s-management-tui](https://github.com/DataDynamics/k3s-management-tui)(Apache 2.0)의 README·설정·스크린샷을 바탕으로 한 TUI 도구 매뉴얼 |

두 샘플에 공통으로 쓰인 작성 방식은 다음과 같습니다.

* 여러 장을 묶는 **부(part)** 는 문서 파일(`sample-manual.adoc`)에 `= N부 — 제목` 1단계 제목으로 둡니다.
* **장(chapter)** 은 장 디렉터리(`manual/<부>/`, `k3stui/`)의 파일 하나에 `==` 2단계 제목으로 씁니다.
* 부록은 `[appendix]` 를 붙인 장으로 마지막에 둡니다.
* 제품명, 포트, URL 같은 반복 값은 문서 헤더의 속성(`{product}`, `{api-port}` 등)으로 한곳에서 관리합니다.
  코드 블록에서 속성을 쓰려면 `subs="+attributes"` 를 붙입니다(`+` 를 빼면 콜아웃 등 기본 치환이 꺼집니다).
* 키 입력은 `kbd:[Ctrl+R]`, 메뉴는 `menu:관리[사용자]`, 버튼은 `btn:[저장]` 매크로로 씁니다 (`:experimental:` 속성 필요).
* 화면 그림은 `image::k3stui/dashboard.png[설명,pdfwidth=100%]` 처럼 PDF 에서의 폭을 함께 지정합니다.
* SVG 그림에 한글을 쓰면 `font-family` 에 PDF 테마의 본문 글꼴(`KaiGen Gothic KR`)을 먼저 적어야 PDF 에서 글자가 깨지지 않습니다.

새 문서를 추가하려면 `src/docs/asciidoc/` 에 `.adoc` 파일을 만들고, 헤더에서 `include::_attributes-ko.adoc[]` 로 공통 속성을 가져온 뒤,
`build.gradle` 의 `sources { include ... }` 목록(HTML, PDF 두 곳)에 파일 이름을 추가합니다.

## 새 장 추가하기

1. `src/docs/asciidoc/chapters/` 에 파일을 만듭니다. 파일 이름은 소문자와 하이픈만 사용합니다.
2. 첫 줄에 `[[anchor]]` 앵커와 `== 제목` 을 씁니다.
3. `index.adoc` 끝에 `include::chapters/<파일>.adoc[]` 을 추가합니다.

## 빌드 실패 기준

깨진 상호 참조, 정의되지 않은 속성, 없는 include 파일, 없는 그림은 모두 **빌드 실패**로 처리됩니다
(`failureLevel = 'WARN'`, `attribute-missing = warn`).
빌드 로그의 `asciidoctor: WARNING` 줄에서 원인을 확인하십시오.

코드 블록 안에 `include::` 나 `<<xref>>` 를 예시로 적을 때는 `\include::`, `` `+<<xref>>+` `` 처럼 이스케이프해야 합니다.

## PDF 테마 바꾸기

`src/docs/theme/DataDynamics-theme.yml` 은 Asciidoctor PDF 기본 테마를 상속(`extends: default`)합니다.
색상은 `brand` 섹션의 `primary`, `secondary`, `accent` 값만 바꾸면 제목, 링크, 표 머리글, 표지에 함께 반영됩니다.
글꼴을 바꾸려면 `font.catalog` 에 글꼴 파일을 등록하고 `base.font_family` 를 수정하십시오.
굵은 글꼴 파일이 따로 없는 글꼴은 제목이 굵게 표시되지 않으니 Regular/Bold 쌍이 있는 글꼴을 사용하십시오.

다른 테마를 쓰려면 `build.gradle` 의 `asciidoctorPdf` 블록에서 `pdf-theme` 값을 바꿉니다.
예를 들어 대체 테마를 쓰려면 `DataDynamics-theme.yml` 을 `KaiGenGothicKR-theme.yml` 로 바꾸십시오.
`KaiGenGothicKR-theme.yml` 은 브랜드 색상 없이 회색 위주로 꾸민 테마로, 본문 글꼴이 작고(9pt) 코드는 Roboto Mono 로 표시합니다.

## 글꼴

`src/docs/theme/` 에는 두 PDF 테마의 `font.catalog` 가 참조하는 글꼴만 들어 있습니다.

| 글꼴 | 파일 | 사용처 |
|---|---|---|
| KaiGen Gothic KR | `KaiGenGothicKR-Regular`, `-Bold`, `-Regular-Italic`, `-Bold-Italic` | 두 테마의 본문, 제목 |
| D2Coding | `D2Coding`, `D2CodingBold` | DataDynamics 테마의 코드 |
| Roboto Mono | `RobotoMono-Regular`, `-Bold`, `-Italic`, `-BoldItalic` | KaiGenGothicKR 테마의 코드 |

Noto Serif 와 M+ 1mn 은 Asciidoctor PDF 에 포함된 글꼴을 사용합니다 (`pdf-fontsdir` 의 `GEM_FONTS_DIR`).
두 테마 모두 Noto Serif 를 대체 글꼴로 지정해, Asciidoctor PDF 가 넣는 특수 공백(U+200B 제로폭 공백, U+202F 좁은 줄바꿈 없는 공백)을 처리합니다.

새 글꼴을 추가할 때는 파일을 `src/docs/theme/` 에 넣고 테마의 `font.catalog` 에 등록하십시오.
catalog 에 등록하지 않은 글꼴 파일은 사용되지 않으니 저장소에 넣지 마십시오.
