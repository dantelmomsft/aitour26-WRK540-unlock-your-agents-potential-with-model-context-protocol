## आप क्या सीखेंगे

इस लैब में, आप बिक्री डेटा का विश्लेषण करने और प्राकृतिक भाषा का उपयोग करके चार्ट बनाने के लिए Code Interpreter को सक्षम करेंगे।

## परिचय

इस लैब में, आप Azure AI Agent को दो टूल्स के साथ विस्तारित करेंगे:

- **Code Interpreter:** एजेंट को डेटा विश्लेषण और विज़ुअलाइज़ेशन के लिए Python कोड जेनरेट करने और चलाने देता है।
- **MCP Server tools:** एजेंट को MCP Tools का उपयोग करके बाहरी डेटा स्रोतों तक पहुंचने की अनुमति देते हैं, हमारे मामले में PostgreSQL डेटाबेस में डेटा।

## लैब अभ्यास

### Code Interpreter सक्षम करना

इस लैब में, आप Zava के रिटेल बिक्री डेटा का विश्लेषण करने के लिए LLM द्वारा जेनरेट किए गए Python कोड को निष्पादित करने के लिए Code Interpreter को सक्षम करेंगे।

=== "Python"

    1. `app.py` **खोलें**।
    2. `AgentManager` क्लास की `_setup_agent_tools` method में एजेंट के toolset में Code Interpreter tool जोड़ने वाली लाइन को **अनकमेंट** करें। यह लाइन वर्तमान में शुरुआत में `#` के साथ commented out है।:

        ```python
        # code_interpreter = CodeInterpreterTool()
        # self.toolset.add(code_interpreter)
        ```

    3. `app.py` फाइल में कोड की **समीक्षा** करें। आप देखेंगे कि Code Interpreter और MCP Server tools `AgentManager` क्लास की `_setup_agent_tools` method में एजेंट के toolset में जोड़े गए हैं।

        ```python

        अनकमेंट करने के बाद, आपका कोड इस प्रकार दिखना चाहिए:

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

1. नीचे दिए गए टेक्स्ट को क्लिपबोर्ड पर कॉपी करें:

    ```text
    Debug: Select and Start Debugging
    ```

2. VS Code Command Palette खोलने के लिए <kbd>F1</kbd> दबाएं।
3. Command Palette में टेक्स्ट पेस्ट करें और **Debug: Select and Start Debugging** का चयन करें।
4. सूची से **🔁🤖Debug Compound: Agent and MCP (stdio)** का चयन करें। यह एजेंट ऐप और वेब चैट क्लाइंट शुरू करेगा।

## Agent Web Chat Client खोलना

1. नीचे दिए गए टेक्स्ट को क्लिपबोर्ड पर कॉपी करें:

    ```text
    Open Port in Browser
    ```

2. VS Code Command Palette खोलने के लिए <kbd>F1</kbd> दबाएं।
3. Command Palette में टेक्स्ट पेस्ट करें और **Open Port in Browser** का चयन करें।
4. सूची से **8005** का चयन करें। यह आपके ब्राउज़र में एजेंट वेब चैट क्लाइंट खोलेगा।

### Agent के साथ बातचीत शुरू करना

वेब चैट क्लाइंट से, आप एजेंट के साथ बातचीत शुरू कर सकते हैं। एजेंट Zava के बिक्री डेटा के बारे में सवालों के जवाब देने और Code Interpreter का उपयोग करके विज़ुअलाइज़ेशन जेनरेट करने के लिए डिज़ाइन किया गया है।

1. उत्पाद बिक्री विश्लेषण। चैट में निम्नलिखित प्रश्न को कॉपी और पेस्ट करें:

    ```text
    Show the top 10 products by revenue by store for the last quarter
    ```

    एक क्षण बाद, एजेंट प्रत्येक स्टोर के लिए राजस्व के आधार पर शीर्ष 10 उत्पादों को दिखाने वाली तालिका के साथ प्रतिसाद देगा।

    !!! info
        एजेंट डेटा प्राप्त करने और इसे तालिका में प्रदर्शित करने के लिए LLM तीन MCP Server tools का उपयोग करता है:

           1. **get_current_utc_date()**: वर्तमान दिनांक और समय प्राप्त करता है ताकि एजेंट वर्तमान दिनांक के सापेक्ष पिछली तिमाही निर्धारित कर सके।
           2. **get_multiple_table_schemas()**: LLM द्वारा वैध SQL जेनरेट करने के लिए आवश्यक डेटाबेस में तालिकाओं के स्कीमा प्राप्त करता है।
           3. **execute_sales_query**: PostgreSQL डेटाबेस से पिछली तिमाही के लिए राजस्व के आधार पर शीर्ष 10 उत्पादों को प्राप्त करने के लिए SQL क्वेरी निष्पादित करता है।

2. पाई चार्ट जेनरेट करना। चैट में निम्नलिखित प्रश्न को कॉपी और पेस्ट करें:

    ```text
    Show sales by store as a pie chart for this financial year
    ```

    एजेंट वर्तमान वित्तीय वर्ष के लिए स्टोर के आधार पर बिक्री वितरण दिखाने वाले पाई चार्ट के साथ प्रतिसाद देगा।

    !!! info
        यह जादू जैसा लग सकता है, तो इसे काम करने के लिए पर्दे के पीछे क्या हो रहा है?

        Foundry Agent Service निम्नलिखित चरणों का आयोजन करता है:

        1. पिछले प्रश्न की तरह, एजेंट निर्धारित करता है कि क्या उसके पास क्वेरी के लिए आवश्यक table schemas हैं। यदि नहीं, तो यह वर्तमान दिनांक और डेटाबेस स्कीमा प्राप्त करने के लिए **get_multiple_table_schemas()** tools का उपयोग करता है।
        2. एजेंट फिर बिक्री प्राप्त करने के लिए **execute_sales_query** tool का उपयोग करता है
        3. वापस किए गए डेटा का उपयोग करके, LLM पाई चार्ट बनाने के लिए Python कोड लिखता है।
        4. अंत में, Code Interpreter चार्ट जेनरेट करने के लिए Python कोड निष्पादित करता है।

3. Code Interpreter को एक्शन में देखने के लिए Zava बिक्री डेटा के बारे में प्रश्न पूछना जारी रखें। यहां कुछ फॉलो-अप प्रश्न हैं जिन्हें आप आज़माना चाह सकते हैं:

    - ```निर्धारित करें कि कौन से उत्पाद या श्रेणियां बिक्री चलाते हैं। Bar Chart के रूप में दिखाएं।```
    - ```एक shock event (जैसे, एक क्षेत्र में 20% बिक्री गिरावट) का global sales distribution पर क्या प्रभाव होगा? Grouped Bar Chart के रूप में दिखाएं।```
        - फॉलो अप करें ```यदि shock event 50% था तो क्या होगा?```
    - ```कौन से क्षेत्रों में औसत से ऊपर या नीचे बिक्री है? औसत से विचलन के साथ Bar Chart के रूप में दिखाएं।```
    - ```कौन से क्षेत्रों में औसत से ऊपर या नीचे छूट है? औसत से विचलन के साथ Bar Chart के रूप में दिखाएं।```
    - ```confidence intervals का अनुमान लगाने के लिए Monte Carlo simulation का उपयोग करके क्षेत्र के आधार पर भविष्य की बिक्री का अनुकरण करें। vivid colors का उपयोग करके Confidence Bands के साथ Line के रूप में दिखाएं।```

<!-- ## Agent App बंद करना

1. VS Code editor पर वापस स्विच करें।
1. एजेंट ऐप को रोकने के लिए <kbd>Shift + F5</kbd> दबाएं। -->

## Agent App को चलता छोड़ें

एजेंट ऐप को चलता छोड़ें क्योंकि आप अगली लैब में इसका उपयोग करके एजेंट को अधिक टूल्स और क्षमताओं के साथ विस्तारित करेंगे।

*GitHub Copilot और GPT-4o का उपयोग करके अनुवादित।*
