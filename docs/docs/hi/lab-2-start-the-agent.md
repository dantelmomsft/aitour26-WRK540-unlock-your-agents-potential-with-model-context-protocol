## आप क्या सीखेंगे

इस लैब में, आप सेल्स डेटा का विश्लेषण करने और प्राकृतिक भाषा का उपयोग करके चार्ट बनाने के लिए Code Interpreter को सक्षम करेंगे।

## परिचय

इस लैब में, आप Azure AI Agent को दो टूल्स के साथ विस्तारित करेंगे:

- **Code Interpreter:** एजेंट को डेटा विश्लेषण और विज़ुअलाइज़ेशन के लिए Python कोड जेनरेट करने और चलाने देता है।
- **MCP Server tools:** एजेंट को MCP Tools का उपयोग करके बाहरी डेटा स्रोतों तक पहुंच की अनुमति देते हैं, हमारे मामले में PostgreSQL डेटाबेस में डेटा।

## लैब एक्सरसाइज़

### Code Interpreter को सक्षम करना

इस लैब में, आप Zava के रिटेल सेल्स डेटा का विश्लेषण करने के लिए LLM द्वारा जेनरेट किए गए Python कोड को execute करने के लिए Code Interpreter को सक्षम करेंगे।

=== "Python"

    1. `app.py` को **खोलें**।
    2. `AgentManager` क्लास के `_setup_agent_tools` method में एजेंट के toolset में Code Interpreter tool जोड़ने वाली लाइन को **Uncomment** करें। यह लाइन वर्तमान में शुरुआत में `#` के साथ commented out है:

        ```python
        # code_interpreter = CodeInterpreterTool()
        # self.toolset.add(code_interpreter)
        ```

    3. `app.py` फाइल में कोड की **समीक्षा** करें। आप देखेंगे कि Code Interpreter और MCP Server tools को `AgentManager` क्लास के `_setup_agent_tools` method में एजेंट के toolset में जोड़ा गया है।

        ```python

        Uncommenting के बाद, आपका कोड इस प्रकार दिखना चाहिए:

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

    TBD

## Agent App शुरू करना

1. नीचे दिए गए टेक्स्ट को क्लिपबोर्ड में कॉपी करें:

    ```text
    Debug: Select and Start Debugging
    ```

2. VS Code Command Palette खोलने के लिए <kbd>F1</kbd> दबाएं।
3. टेक्स्ट को Command Palette में पेस्ट करें और **Debug: Select and Start Debugging** सेलेक्ट करें।
4. सूची से **🔁🤖Debug Compound: Agent and MCP (stdio)** सेलेक्ट करें। यह एजेंट ऐप और वेब चैट क्लाइंट शुरू करेगा।

## Agent Web Chat Client खोलना

1. नीचे दिए गए टेक्स्ट को क्लिपबोर्ड में कॉपी करें:

    ```text
    Open Port in Browser
    ```

2. VS Code Command Palette खोलने के लिए <kbd>F1</kbd> दबाएं।
3. टेक्स्ट को Command Palette में पेस्ट करें और **Open Port in Browser** सेलेक्ट करें।
4. सूची से **8005** सेलेक्ट करें। यह आपके ब्राउज़र में एजेंट वेब चैट क्लाइंट खोलेगा।

### Agent के साथ बातचीत शुरू करना

वेब चैट क्लाइंट से, आप एजेंट के साथ बातचीत शुरू कर सकते हैं। एजेंट को Zava के सेल्स डेटा के बारे में प्रश्नों के उत्तर देने और Code Interpreter का उपयोग करके विज़ुअलाइज़ेशन जेनरेट करने के लिए डिज़ाइन किया गया है।

1. प्रोडक्ट सेल्स एनालिसिस। चैट में निम्नलिखित प्रश्न को कॉपी और पेस्ट करें:

    ```text
    पिछली तिमाही के लिए स्टोर द्वारा revenue के हिसाब से टॉप 10 प्रोडक्ट्स दिखाएं
    ```

    कुछ देर बाद, एजेंट प्रत्येक स्टोर के लिए revenue के हिसाब से टॉप 10 प्रोडक्ट्स दिखाने वाली तालिका के साथ जवाब देगा।

    !!! info
        एजेंट डेटा fetch करने और तालिका में प्रदर्शित करने के लिए LLM तीन MCP Server tools का उपयोग करता है:

           1. **get_current_utc_date()**: वर्तमान date और time प्राप्त करता है ताकि एजेंट वर्तमान तारीख के सापेक्ष पिछली तिमाही निर्धारित कर सके।
           2. **get_multiple_table_schemas()**: डेटाबेस में tables के schemas प्राप्त करता है जो LLM को valid SQL जेनरेट करने के लिए आवश्यक है।
           3. **execute_sales_query**: PostgreSQL डेटाबेस से पिछली तिमाही के लिए revenue के हिसाब से टॉप 10 प्रोडक्ट्स fetch करने के लिए SQL क्वेरी execute करता है।

2. पाई चार्ट जेनरेट करना। चैट में निम्नलिखित प्रश्न को कॉपी और पेस्ट करें:

    ```text
    इस वित्तीय वर्ष के लिए स्टोर द्वारा सेल्स को पाई चार्ट के रूप में दिखाएं
    ```

    एजेंट वर्तमान वित्तीय वर्ष के लिए स्टोर द्वारा सेल्स वितरण दिखाने वाले पाई चार्ट के साथ जवाब देगा।

    !!! info
        यह जादू जैसा लग सकता है, तो इसे काम करने के लिए पर्दे के पीछे क्या हो रहा है?

        Foundry Agent Service निम्नलिखित चरणों का orchestration करती है:

        1. पिछले प्रश्न की तरह, एजेंट निर्धारित करता है कि क्या उसके पास क्वेरी के लिए आवश्यक table schemas हैं। यदि नहीं, तो यह वर्तमान date और database schema प्राप्त करने के लिए **get_multiple_table_schemas()** tools का उपयोग करता है।
        2. एजेंट फिर सेल्स fetch करने के लिए **execute_sales_query** tool का उपयोग करता है।
        3. returned data का उपयोग करके, LLM Pie Chart बनाने के लिए Python कोड लिखता है।
        4. अंत में, Code Interpreter चार्ट जेनरेट करने के लिए Python कोड को execute करता है।

3. Code Interpreter को action में देखने के लिए Zava सेल्स डेटा के बारे में प्रश्न पूछना जारी रखें। यहां कुछ follow-up प्रश्न हैं जो आप कोशिश कर सकते हैं:

    - ```निर्धारित करें कि कौन से प्रोडक्ट्स या categories सेल्स drive करते हैं। Bar Chart के रूप में दिखाएं।```
    - ```एक shock event (जैसे, एक region में 20% सेल्स drop) का global सेल्स distribution पर क्या प्रभाव होगा? Grouped Bar Chart के रूप में दिखाएं।```
        - Follow up के साथ ```यदि shock event 50% था तो क्या होगा?```
    - ```कौन से regions में average से ऊपर या नीचे सेल्स हैं? Deviation from Average के साथ Bar Chart के रूप में दिखाएं।```
    - ```कौन से regions में average से ऊपर या नीचे discounts हैं? Deviation from Average के साथ Bar Chart के रूप में दिखाएं।```
    - ```Confidence intervals का अनुमान लगाने के लिए Monte Carlo simulation का उपयोग करके region द्वारा future सेल्स का simulation करें। vivid colors का उपयोग करके Confidence Bands के साथ Line के रूप में दिखाएं।```

<!-- ## Stop the Agent App

1. Switch back to the VS Code editor.
1. Press <kbd>Shift + F5</kbd> to stop the agent app. -->

## Agent App को चालू रखना

Agent app को चालू रखें क्योंकि आप इसे अगली लैब में अधिक टूल्स और क्षमताओं के साथ एजेंट को विस्तारित करने के लिए उपयोग करेंगे।

*GitHub Copilot का उपयोग करके अनुवादित।*
