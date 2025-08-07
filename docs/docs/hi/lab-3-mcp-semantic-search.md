## आप क्या सीखेंगे

इस लैब में, आप Model Context Protocol (MCP) और [PostgreSQL Vector](https://github.com/pgvector/pgvector){:target="_blank"} extension enabled के साथ PostgreSQL डेटाबेस का उपयोग करके Azure AI Agent में semantic search क्षमताओं को सक्षम करते हैं।

## परिचय

यह लैब Model Context Protocol (MCP) और PostgreSQL का उपयोग करके Azure AI Agent को semantic search के साथ अपग्रेड करती है। प्रोडक्ट नाम और विवरणों को OpenAI embedding model (text-embedding-3-small) के साथ vectors में बदला गया और डेटाबेस में store किया गया। यह एजेंट को user intent समझने और अधिक सटीक प्रतिक्रियाएं प्रदान करने में सक्षम बनाता है।

## लैब एक्सरसाइज़

पिछली लैब से आप एजेंट से सेल्स डेटा के बारे में प्रश्न पूछ सकते हैं, लेकिन यह exact matches तक सीमित था। इस लैब में, आप Model Context Protocol (MCP) का उपयोग करके semantic search implement करके एजेंट की क्षमताओं को विस्तारित करते हैं। यह एजेंट को उन क्वेरीज़ को समझने और उनका जवाब देने की अनुमति देगा जो exact matches नहीं हैं, जटिल प्रश्नों के साथ उपयोगकर्ताओं की सहायता करने की इसकी क्षमता में सुधार करेगा।

1. अपने ब्राउज़र में Web Chat tab में निम्नलिखित प्रश्न को पेस्ट करें:

    ```text
    18A breakers के साथ विभिन्न स्टोर्स का प्रदर्शन कैसा रहा?
    ```

    एजेंट जवाब देता है: "मुझे हमारे रिकॉर्ड्स में 18A breakers के लिए कोई सेल्स डेटा नहीं मिला। 😱 हालांकि, यहां कुछ similar प्रोडक्ट्स के सुझाव हैं जिन्हें आप explore करना चाह सकते हैं।" ऐसा इसलिए होता है क्योंकि एजेंट केवल keywords द्वारा matching queries पर निर्भर करता है और आपके प्रश्न के semantic meaning को नहीं समझता। LLM अभी भी किसी भी प्रोडक्ट संदर्भ से educated प्रोडक्ट सुझाव दे सकता है जो उसके पास पहले से हो।

## Semantic Search को Implement करना

इस section में, आप एजेंट की क्षमताओं को बढ़ाने के लिए Model Context Protocol (MCP) का उपयोग करके semantic search implement करेंगे।

1. VS Code Command Palette **खोलने** के लिए <kbd>F1</kbd> दबाएं।
2. **Open File** टाइप करें और **File: Open File...** सेलेक्ट करें।
3. file picker में निम्नलिखित path को **पेस्ट** करें और <kbd>Enter</kbd> दबाएं:

    ```text
    /workspace/src/python/mcp_server/sales_analysis/sales_analysis.py
    ```

4. लगभग line 100 तक scroll down करें और `semantic_search_products` method को खोजें। यह method सेल्स डेटा पर semantic search perform करने के लिए जिम्मेदार है। आप देखेंगे कि **@mcp.tool()** decorator को commented out किया गया है। यह decorator method को MCP tool के रूप में register करने के लिए उपयोग किया जाता है, जिससे एजेंट द्वारा इसे call किया जा सकता है।

5. Line की शुरुआत में `#` को हटाकर `@mcp.tool()` decorator को uncomment करें। यह semantic search tool को सक्षम करेगा।

    ```python
    # @mcp.tool()
    async def semantic_search_products(
        ctx: Context,
        query_description: Annotated[str, Field(
        ...
    ```

6. इसके बाद, आपको Agent instructions को semantic search tool का उपयोग करने के लिए सक्षम करना होगा। `app.py` फाइल पर वापस switch करें।
7. लगभग line 30 तक scroll down करें और `# INSTRUCTIONS_FILE = "instructions/mcp_server_tools_with_semantic_search.txt"` line को खोजें।
8. शुरुआत में `#` को हटाकर line को uncomment करें। यह एजेंट को semantic search tool का उपयोग करने में सक्षम करेगा।

    ```python
    INSTRUCTIONS_FILE = "instructions/mcp_server_tools_with_semantic_search.txt"
    ```

## Agent Instructions की समीक्षा करना

1. VS Code Command Palette खोलने के लिए <kbd>F1</kbd> दबाएं।
2. **Open File** टाइप करें और **File: Open File...** सेलेक्ट करें।
3. file picker में निम्नलिखित path को पेस्ट करें और <kbd>Enter</kbd> दबाएं:

    ```text
    /workspace/src/shared/instructions/mcp_server_tools_with_semantic_search.txt
    ```

4. फाइल में instructions की समीक्षा करें। ये instructions एजेंट को सेल्स डेटा के बारे में प्रश्नों के उत्तर देने के लिए semantic search tool का उपयोग करने का निर्देश देते हैं।

## Semantic Search Tool के साथ Agent App शुरू करना

1. <kbd>Shift + F5</kbd> दबाकर वर्तमान एजेंट ऐप को **रोकें**।
2. <kbd>F5</kbd> दबाकर एजेंट ऐप को **पुनः आरंभ** करें। यह updated instructions और semantic search tool enabled के साथ एजेंट को शुरू करेगा।
3. अपने ब्राउज़र में **Web Chat** tab पर वापस switch करें।
4. चैट में निम्नलिखित प्रश्न दर्ज करें:

    ```text
    18A breakers के साथ विभिन्न स्टोर्स का प्रदर्शन कैसा रहा?
    ```

    एजेंट अब प्रश्न के semantic meaning को समझता है और relevant सेल्स डेटा के साथ तदनुसार जवाब देता है।

    !!! info "नोट"
        MCP Semantic Search tool इस प्रकार काम करता है:

        1. प्रश्न को प्रोडक्ट विवरणों के समान OpenAI embedding model (text-embedding-3-small) का उपयोग करके vector में बदला जाता है।
        2. इस vector का उपयोग PostgreSQL डेटाबेस में similar प्रोडक्ट vectors खोजने के लिए किया जाता है।
        3. एजेंट को परिणाम प्राप्त होते हैं और वे उनका उपयोग response जेनरेट करने के लिए करते हैं।

*GitHub Copilot का उपयोग करके अनुवादित।*
