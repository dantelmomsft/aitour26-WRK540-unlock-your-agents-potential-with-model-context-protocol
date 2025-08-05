## आप क्या सीखेंगे

इस लैब में, आप Model Context Protocol (MCP) और PostgreSQL डेटाबेस का उपयोग करके Azure AI Agent में semantic search क्षमताओं को सक्षम करते हैं।

## परिचय

यह लैब Model Context Protocol (MCP) और PostgreSQL का उपयोग करके Azure AI Agent को semantic search के साथ अपग्रेड करती है। उत्पाद नाम और विवरण को OpenAI embedding model (text-embedding-3-small) के साथ vectors में रूपांतरित किया गया और डेटाबेस में संग्रहीत किया गया। यह एजेंट को उपयोगकर्ता के इरादे को समझने और अधिक सटीक प्रतिक्रियाएं प्रदान करने की अनुमति देता है।

## लैब अभ्यास

पिछली लैब से आप एजेंट से बिक्री डेटा के बारे में सवाल पूछ सकते हैं, लेकिन यह सटीक मैच तक सीमित था। इस लैब में, आप Model Context Protocol (MCP) का उपयोग करके semantic search को लागू करके एजेंट की क्षमताओं को बढ़ाते हैं। यह एजेंट को उन क्वेरीज़ को समझने और उनका जवाब देने की अनुमति देगा जो सटीक मैच नहीं हैं, अधिक जटिल प्रश्नों के साथ उपयोगकर्ताओं की सहायता करने की इसकी क्षमता में सुधार करता है।

1. अपने ब्राउज़र में Web Chat टैब में निम्नलिखित प्रश्न पेस्ट करें:

    ```text
    How did different stores perform with 18A breakers?
    ```

    एजेंट प्रतिक्रिया देता है: "I couldn't find any sales data for 18A breakers in our records. 😱 However, here are some suggestions for similar products you might want to explore." ऐसा इसलिए होता है क्योंकि एजेंट केवल keywords द्वारा matching queries पर निर्भर करता है और आपके प्रश्न के semantic अर्थ को नहीं समझता। LLM अभी भी किसी भी उत्पाद संदर्भ से शिक्षित उत्पाद सुझाव दे सकता है जो उसके पास पहले से हो सकता है।

## Semantic Search लागू करना

इस अनुभाग में, आप एजेंट की क्षमताओं को बढ़ाने के लिए Model Context Protocol (MCP) का उपयोग करके semantic search को लागू करेंगे।

1. VS Code Command Palette **खोलने** के लिए <kbd>F1</kbd> दबाएं।
2. **Open File** टाइप करें और **File: Open File...** का चयन करें।
3. फाइल पिकर में निम्नलिखित पाथ **पेस्ट** करें और <kbd>Enter</kbd> दबाएं:

    ```text
    /workspace/src/python/mcp_server/sales_analysis/sales_analysis.py
    ```

4. लगभग लाइन 100 तक नीचे स्क्रॉल करें और `semantic_search_products` method को देखें। यह method बिक्री डेटा पर semantic search करने के लिए जिम्मेदार है। आप देखेंगे कि **@mcp.tool()** decorator commented out है। यह decorator method को MCP tool के रूप में register करने के लिए उपयोग किया जाता है, जिससे एजेंट इसे कॉल कर सकता है।

5. लाइन की शुरुआत में `#` को हटाकर `@mcp.tool()` decorator को uncomment करें। यह semantic search tool को सक्षम करेगा।

    ```python
    # @mcp.tool()
    async def semantic_search_products(
        ctx: Context,
        query_description: Annotated[str, Field(
        ...
    ```

6. इसके बाद, आपको semantic search tool का उपयोग करने के लिए Agent instructions को सक्षम करना होगा। `app.py` फाइल पर वापस स्विच करें।
7. लगभग लाइन 30 तक नीचे स्क्रॉल करें और `# INSTRUCTIONS_FILE = "instructions/mcp_server_tools_with_semantic_search.txt"` लाइन खोजें।
8. शुरुआत में `#` को हटाकर लाइन को uncomment करें। यह एजेंट को semantic search tool का उपयोग करने की अनुमति देगा।

    ```python
    INSTRUCTIONS_FILE = "instructions/mcp_server_tools_with_semantic_search.txt"
    ```

## Agent Instructions की समीक्षा

1. VS Code Command Palette खोलने के लिए <kbd>F1</kbd> दबाएं।
2. **Open File** टाइप करें और **File: Open File...** का चयन करें।
3. फाइल पिकर में निम्नलिखित पाथ पेस्ट करें और <kbd>Enter</kbd> दबाएं:

    ```text
    /workspace/src/shared/instructions/mcp_server_tools_with_semantic_search.txt
    ```

4. फाइल में निर्देशों की समीक्षा करें। ये निर्देश एजेंट को बिक्री डेटा के बारे में सवालों के जवाब देने के लिए semantic search tool का उपयोग करने के लिए निर्देश देते हैं।

## Semantic Search Tool के साथ Agent App शुरू करना

1. <kbd>Shift + F5</kbd> दबाकर वर्तमान एजेंट ऐप को **रोकें**।
2. <kbd>F5</kbd> दबाकर एजेंट ऐप को **पुनः आरंभ** करें। यह एजेंट को अपडेट किए गए निर्देशों और सक्षम semantic search tool के साथ शुरू करेगा।
3. अपने ब्राउज़र में **Web Chat** टैब पर वापस स्विच करें।
4. चैट में निम्नलिखित प्रश्न दर्ज करें:

    ```text
    How did different stores perform with 18A breakers?
    ```

    एजेंट अब प्रश्न के semantic अर्थ को समझता है और संबंधित बिक्री डेटा के साथ तदनुसार प्रतिक्रिया देता है।

    !!! info "नोट"
        MCP Semantic Search tool निम्नलिखित तरीके से काम करता है:

        1. प्रश्न को उत्पाद विवरण के समान OpenAI embedding model (text-embedding-3-small) का उपयोग करके vector में रूपांतरित किया जाता है।
        2. इस vector का उपयोग PostgreSQL डेटाबेस में समान उत्पाद vectors खोजने के लिए किया जाता है।
        3. एजेंट परिणाम प्राप्त करता है और उनका उपयोग प्रतिक्रिया जेनरेट करने के लिए करता है।

*GitHub Copilot और GPT-4o का उपयोग करके अनुवादित।*
