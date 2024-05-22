### Adım 1: Node.js ve Express Kurulumu
Öncelikle, Node.js'in sisteminizde kurulu olduğundan emin olun. Daha sonra bir proje dizini oluşturup gerekli bağımlılıkları yükleyin.

1. **Proje Dizini Oluşturma ve Gerekli Bağımlılıkları Kurma:**
   ```sh
   mkdir voice-call-panel
   cd voice-call-panel
   npm init -y
   npm install express node-fetch
   ```

### Adım 2: API Kodu Yazma
`index.js` adında bir dosya oluşturun ve aşağıdaki kodu bu dosyaya yapıştırın:

```javascript
const express = require('express');
const fetch = require('node-fetch');
const app = express();
app.use(express.json());

app.post('/makeCall', async (req, res) => {
    const { text, toNumber } = req.body;
    try {
        const audioUrl = await textToSpeech(text);
        const response = await makeCall(toNumber, audioUrl);
        res.json(response);
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

async function textToSpeech(text) {
    const response = await fetch('https://api.elevenlabs.io/v1/text-to-speech', {
        method: 'POST',
        headers: {
            'Authorization': 'Bearer YOUR_ELEVENLABS_API_KEY',
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({ text: text, voice: 'en_us_male' })
    });
    const data = await response.json();
    return data.audio_url;
}

async function makeCall(toNumber, audioUrl) {
    const response = await fetch('https://api.kobikom.com/call', {
        method: 'POST',
        headers: {
            'Authorization': 'Bearer YOUR_KOBIKOM_API_KEY',
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({ from: 'YOUR_PHONE_NUMBER', to: toNumber, audio_url: audioUrl })
    });
    const data = await response.json();
    return data;
}

app.listen(3000, () => {
    console.log('Server is running on port 3000');
});
```

### Adım 3: Sunucuyu Başlatma
Terminalde şu komutu çalıştırarak sunucuyu başlatın:
```sh
node index.js
```

### Adım 4: HTML, CSS ve JavaScript Dosyalarını Oluşturma
CodePen veya benzeri bir platformda bu dosyaları oluşturun ve aşağıdaki kodları yapıştırın.

#### HTML
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Voice Call Panel</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="container">
        <h1>Voice Call Panel</h1>
        <form id="callForm">
            <label for="text">Text:</label>
            <input type="text" id="text" name="text" required><br><br>
            <label for="to_number">To Number:</label>
            <input type="text" id="to_number" name="to_number" required><br><br>
            <button type="submit">Make Call</button>
        </form>
        <div id="response"></div>
    </div>
    <script src="script.js"></script>
</body>
</html>
```

#### CSS (styles.css)
```css
body {
    font-family: Arial, sans-serif;
    background: #f0f2f5;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    margin: 0;
}

.container {
    background: #fff;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    max-width: 400px;
    width: 100%;
    text-align: center;
}

h1 {
    color: #333;
}

label {
    display: block;
    margin-bottom: 8px;
    font-weight: bold;
}

input {
    width: calc(100% - 22px);
    padding: 10px;
    margin-bottom: 15px;
    border: 1px solid #ccc;
    border-radius: 4px;
}

button {
    background: #007bff;
    color: #fff;
    border: none;
    padding: 10px 20px;
    border-radius: 4px;
    cursor: pointer;
    transition: background 0.3s;
}

button:hover {
    background: #0056b3;
}

#response {
    margin-top: 20px;
    color: #555;
}
```

#### JavaScript (script.js)
```javascript
document.getElementById('callForm').addEventListener('submit', async function(event) {
    event.preventDefault();
    const text = document.getElementById('text').value;
    const toNumber = document.getElementById('to_number').value;

    try {
        const audioUrl = await textToSpeech(text);
        const response = await makeCall(toNumber, audioUrl);
        document.getElementById('response').innerText = JSON.stringify(response);
    } catch (error) {
        document.getElementById('response').innerText = 'Error: ' + error.message;
    }
});

async function textToSpeech(text) {
    const response = await fetch('https://api.elevenlabs.io/v1/text-to-speech', {
        method: 'POST',
        headers: {
            'Authorization': 'Bearer YOUR_ELEVENLABS_API_KEY',
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({ text: text, voice: 'en_us_male' })
    });
    const data = await response.json();
    return data.audio_url;
}

async function makeCall(toNumber, audioUrl) {
    const response = await fetch('https://api.kobikom.com/call', {
        method: 'POST',
        headers: {
            'Authorization': 'Bearer YOUR_KOBIKOM_API_KEY',
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({ from: 'YOUR_PHONE_NUMBER', to: toNumber, audio_url: audioUrl })
    });
    const data = await response.json();
    return data;
}
```

### Adım 5: API ve Paneli Birleştirme
Web sunucunuzda çalışan API'yi ve oluşturduğunuz paneli kullanarak arama yapın. Sunucunuz çalıştığı sürece web panelinden istekler gönderilebilir.

### Adım 6: ChatGPT Entegrasyonu
Oluşturduğunuz web paneline ait API'yi, OpenAI'nın GPT-3 veya GPT-4 modeline entegre edin. Bu sayede ChatGPT, kullanıcı taleplerine göre API'yi çağırabilir.

Bu adımları izleyerek, ElevenLabs ve Kobikom API'lerini kullanarak sesli arama yapabilen bir panel oluşturabilir ve bunu ChatGPT ile entegre edebilirsiniz.
