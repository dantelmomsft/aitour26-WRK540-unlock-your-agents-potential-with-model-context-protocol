## 학습할 내용

이 랩에서는 자연어를 사용하여 판매 데이터를 분석하고 차트를 생성하기 위해 코드 인터프리터를 활성화합니다.

## 소개

이 랩에서는 두 가지 도구로 Azure AI 에이전트를 확장합니다:

- **코드 인터프리터:** 에이전트가 데이터 분석 및 시각화를 위한 Python 코드를 생성하고 실행할 수 있게 합니다.
- **MCP 서버 도구:** 에이전트가 MCP 도구를 사용하여 외부 데이터 소스(우리의 경우 PostgreSQL 데이터베이스의 데이터)에 액세스할 수 있게 합니다.

## 랩 실습

### 코드 인터프리터 활성화

이 랩에서는 Zava의 소매 판매 데이터를 분석하기 위해 LLM이 생성한 Python 코드를 실행하도록 코드 인터프리터를 활성화합니다.

=== "Python"

    1. `app.py`를 **엽니다**.
    2. `AgentManager` 클래스의 `_setup_agent_tools` 메서드에서 에이전트의 도구 집합에 코드 인터프리터 도구를 추가하는 줄의 **주석을 해제**합니다. 이 줄은 현재 시작 부분에 `#`으로 주석 처리되어 있습니다.:

        ```python
        # code_interpreter = CodeInterpreterTool()
        # self.toolset.add(code_interpreter)
        ```

    3. `app.py` 파일의 코드를 **검토**합니다. `AgentManager` 클래스의 `_setup_agent_tools` 메서드에서 코드 인터프리터와 MCP 서버 도구가 에이전트의 도구 집합에 추가되는 것을 확인할 수 있습니다.

        ```python

        주석을 해제한 후 코드는 다음과 같아야 합니다:

        ```python
        class AgentManager:
            """Manages Azure AI Agent lifecycle and dependencies."""

            async def _setup_agent_tools(self) -> None:
                """Setup MCP tools and code interpreter."""

                # Enable the code interpreter tool
                code_interpreter = CodeInterpreterTool()
                self.toolset.add(code_interpreter)

                print("Setting up Agent tools...")
                ...
        ```

=== "C#"

    추후 제공 예정

## 에이전트 앱 시작

1. 아래 텍스트를 클립보드에 복사합니다:

    ```text
    Debug: Select and Start Debugging
    ```

2. <kbd>F1</kbd>을 눌러 VS Code 명령 팔레트를 엽니다.
3. 명령 팔레트에 텍스트를 붙여넣고 **Debug: Select and Start Debugging**을 선택합니다.
4. 목록에서 **🔁🤖Debug Compound: Agent and MCP (stdio)**를 선택합니다. 이렇게 하면 에이전트 앱과 웹 채팅 클라이언트가 시작됩니다.

## 에이전트 웹 채팅 클라이언트 열기

1. 아래 텍스트를 클립보드에 복사합니다:

    ```text
    Open Port in Browser
    ```

2. <kbd>F1</kbd>을 눌러 VS Code 명령 팔레트를 엽니다.
3. 명령 팔레트에 텍스트를 붙여넣고 **Open Port in Browser**를 선택합니다.
4. 목록에서 **8005**를 선택합니다. 이렇게 하면 브라우저에서 에이전트 웹 채팅 클라이언트가 열립니다.

### 에이전트와 대화 시작

웹 채팅 클라이언트에서 에이전트와 대화를 시작할 수 있습니다. 에이전트는 Zava의 판매 데이터에 대한 질문에 답변하고 코드 인터프리터를 사용하여 시각화를 생성하도록 설계되었습니다.

1. 제품 판매 분석. 다음 질문을 복사하여 채팅에 붙여넣습니다:

    ```text
    Show the top 10 products by revenue by store for the last quarter
    ```

    잠시 후 에이전트가 각 매장의 수익별 상위 10개 제품을 보여주는 테이블로 응답합니다.

    !!! info
        에이전트는 LLM이 세 개의 MCP 서버 도구를 호출하여 데이터를 가져오고 테이블에 표시합니다:

           1. **get_current_utc_date()**: 현재 날짜와 시간을 가져와서 에이전트가 현재 날짜를 기준으로 지난 분기를 결정할 수 있게 합니다.
           2. **get_multiple_table_schemas()**: LLM이 유효한 SQL을 생성하는 데 필요한 데이터베이스 테이블의 스키마를 가져옵니다.
           3. **execute_sales_query**: PostgreSQL 데이터베이스에서 지난 분기의 수익별 상위 10개 제품을 가져오는 SQL 쿼리를 실행합니다.

2. 파이 차트 생성. 다음 질문을 복사하여 채팅에 붙여넣습니다:

    ```text
    Show sales by store as a pie chart for this financial year
    ```

    에이전트가 현재 회계연도의 매장별 판매 분포를 보여주는 파이 차트로 응답합니다.

    !!! info
        이것이 마법처럼 느껴질 수 있지만, 모든 것이 작동하게 하는 배경에서 무슨 일이 일어나고 있을까요?

        Foundry 에이전트 서비스는 다음 단계를 조율합니다:

        1. 이전 질문과 마찬가지로 에이전트는 쿼리에 필요한 테이블 스키마가 있는지 확인합니다. 없으면 **get_multiple_table_schemas()** 도구를 사용하여 현재 날짜와 데이터베이스 스키마를 가져옵니다.
        2. 그런 다음 에이전트는 **execute_sales_query** 도구를 사용하여 판매 데이터를 가져옵니다
        3. 반환된 데이터를 사용하여 LLM이 파이 차트를 생성하는 Python 코드를 작성합니다.
        4. 마지막으로 코드 인터프리터가 Python 코드를 실행하여 차트를 생성합니다.

3. Zava 판매 데이터에 대해 계속 질문하여 코드 인터프리터가 작동하는 것을 확인하세요. 시도해볼 만한 몇 가지 후속 질문은 다음과 같습니다:

    - ```판매를 주도하는 제품이나 카테고리를 결정하세요. 막대 차트로 보여주세요.```
    - ```충격 이벤트(예: 한 지역에서 20% 판매 감소)가 글로벌 판매 분포에 미치는 영향은 무엇입니까? 그룹화된 막대 차트로 보여주세요.```
        - 후속 질문: ```충격 이벤트가 50%라면 어떨까요?```
    - ```평균보다 높거나 낮은 판매를 기록한 지역은 어디입니까? 평균으로부터의 편차를 보여주는 막대 차트로 표시하세요.```
    - ```평균보다 높거나 낮은 할인을 적용한 지역은 어디입니까? 평균으로부터의 편차를 보여주는 막대 차트로 표시하세요.```
    - ```몬테카를로 시뮬레이션을 사용하여 지역별 미래 판매를 시뮬레이션하여 신뢰 구간을 추정하세요. 생생한 색상을 사용한 신뢰 대역이 있는 선으로 보여주세요.```

<!-- ## 에이전트 앱 중지

1. VS Code 편집기로 다시 전환합니다.
1. <kbd>Shift + F5</kbd>를 눌러 에이전트 앱을 중지합니다. -->

## 에이전트 앱 실행 유지

다음 랩에서 더 많은 도구와 기능으로 에이전트를 확장하는 데 사용할 것이므로 에이전트 앱을 실행 상태로 유지하세요.

*GitHub Copilot을 사용하여 번역되었습니다.*
