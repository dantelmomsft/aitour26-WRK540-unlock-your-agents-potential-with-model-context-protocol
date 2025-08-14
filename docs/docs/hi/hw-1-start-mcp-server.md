## आप क्या सीखेंगे

इस लैब में, आप:

- DevTunnel का उपयोग करके अपने लोकल MCP सर्वर को क्लाउड आधारित एजेंट सेवाओं के लिए एक्सेसिबल बनाएं
- Model Context Protocol के साथ व्यावहारिक प्रयोग के लिए अपना वातावरण सेट करें

## परिचय

Model Context Protocol (MCP) सर्वर LLMs और बाहरी टूल/डेटा स्रोतों के बीच संचार संभालने वाला एक महत्वपूर्ण घटक है। आप MCP सर्वर को लोकल चलाएंगे, लेकिन Azure AI Foundry Agent Service को उससे कनेक्ट होने के लिए इंटरनेट एक्सेस चाहिए। लोकल MCP सर्वर को इंटरनेट से एक्सेसिबल बनाने के लिए आप DevTunnel का उपयोग करेंगे। इससे Agent Service MCP सर्वर से ऐसे संचार कर सकती है मानो वह Azure में परिनियोजित हो।

## MCP के लिए इंटरफ़ेस विकल्प

MCP दो मुख्य इंटरफ़ेस सपोर्ट करता है:

- **Streamable HTTP Transport**: वेब आधारित API और सेवाओं के लिए।
- **Stdio Transport**: लोकल स्क्रिप्ट और कमांड लाइन टूल्स के लिए।

यह लैब Azure AI Foundry Agent Service के साथ इंटीग्रेट करने के लिए Streamable HTTP ट्रांसपोर्ट का उपयोग करता है।

!!! note
    प्रोडक्शन में आप MCP सर्वर को परिनियोजित करते, लेकिन इस वर्कशॉप के लिए आप इसे लोकल डेवलपमेंट वातावरण में चलाएंगे ताकि बिना पूर्ण परिनियोजन के परीक्षण कर सकें।

### MCP सर्वर के लिए DevTunnel शुरू करें

1. नए टर्मिनल में DevTunnel प्रमाणित करें:

    ```bash
    devtunnel login
    ```

1. फिर MCP सर्वर वाले टर्मिनल में DevTunnel होस्ट करें:

    ```bash
    devtunnel host -p 8000 --allow-anonymous
    ```

    आउटपुट में URL होगा जिसकी आवश्यकता एजेंट को कनेक्ट होने हेतु होगी। उदाहरण:

    ```text
    Hosting port: 8000
    Connect via browser: https://<tunnel-id>-8000.aue.devtunnels.ms
    Inspect network activity: https://<tunnel-id>-8000-inspect.aue.devtunnels.ms
    ```

## DevTunnel पर्यावरण चर अपडेट करें

1. **Connect via browser** URL कॉपी करें।
2. वर्कशॉप फ़ोल्डर की `.env` फ़ाइल खोलें।
3. `DEV_TUNNEL_URL` को अपडेट करें:

    ```text
    DEV_TUNNEL_URL=https://<tunnel-id>-8000.aue.devtunnels.ms
    ```

## एजेंट ऐप शुरू करें

1. नीचे का पाठ कॉपी करें:

    ```text
    Debug: Select and Start Debugging
    ```

2. <kbd>F1</kbd> दबाएँ, कमांड पैलेट खोलें।
3. पेस्ट करें और **Debug: Select and Start Debugging** चुनें।
4. **🌎🤖Debug Compound: Agent and MCP (http)** चुनें।

## एजेंट के साथ बातचीत शुरू करें

ब्राउज़र में **Web Chat** टैब पर जाएँ। एजेंट तैयार होना चाहिए।

### DevTunnel के साथ डिबगिंग

आप MCP सर्वर और एजेंट ऐप को डिबग कर सकते हैं।

1. DevTunnel आउटपुट से **Inspect network activity** URL चुनें।
2. नेटवर्क गतिविधि देखें।

ब्रेकप्वाइंट सेट करने के लिए:

1. `mcp_server` फ़ोल्डर में `sales_analysis.py` खोलें।
2. लाइन नंबर के पास गटर में क्लिक कर ब्रेकप्वाइंट सेट करें।
3. निष्पादन पहुँचने पर वेरिएबल देखें, स्टेप करें।

*GitHub Copilot द्वारा अनुवादित.*
