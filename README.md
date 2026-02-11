<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>مساعد الطالب الجامعي</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', 'Tahoma', sans-serif; }
        body { background: #0a0c0e; color: #e8eaed; display: flex; justify-content: center; align-items: center; min-height: 100vh; padding: 15px; }
        .chat-container { max-width: 800px; width: 100%; background: #1e1f22; border-radius: 24px; padding: 25px; box-shadow: 0 10px 40px rgba(0,0,0,0.5); }
        .header { display: flex; align-items: center; gap: 12px; margin-bottom: 25px; border-bottom: 1px solid #3c3f45; padding-bottom: 15px; }
        .avatar { background: #2a6f97; width: 50px; height: 50px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 24px; }
        .title { font-size: 1.5rem; font-weight: bold; color: white; }
        .sub { color: #b0b3b8; font-size: 0.85rem; }
        .chat-box { background: #2c2f33; border-radius: 18px; padding: 20px; margin-bottom: 20px; min-height: 200px; max-height: 400px; overflow-y: auto; }
        .message { margin-bottom: 15px; display: flex; flex-wrap: wrap; }
        .user-message { background: #2a6f97; color: white; padding: 12px 18px; border-radius: 18px 18px 4px 18px; max-width: 80%; margin-left: auto; }
        .bot-message { background: #3c3f45; color: #e8eaed; padding: 12px 18px; border-radius: 18px 18px 18px 4px; max-width: 80%; }
        .input-area { display: flex; gap: 10px; background: #2c2f33; border-radius: 50px; padding: 8px; }
        input { flex: 1; background: transparent; border: none; padding: 15px 20px; color: white; font-size: 1rem; outline: none; }
        button { background: #2a6f97; border: none; color: white; padding: 12px 25px; border-radius: 50px; font-weight: bold; cursor: pointer; }
        button:hover { background: #1f4e6f; }
        .result-item { background: #35393e; border-radius: 12px; padding: 12px; margin-bottom: 8px; border-right: 4px solid #2a6f97; }
        a { color: #8ab4f8; text-decoration: none; }
    </style>
</head>
<body>
<div class="chat-container">
    <div class="header">
        <div class="avatar">🎓</div>
        <div>
            <div class="title">مساعد الطالب الجامعي</div>
            <div class="sub">ذكاء اصطناعي + باحث أكاديمي</div>
        </div>
    </div>
    
    <div id="chatBox" class="chat-box">
        <div class="message">
            <div class="bot-message">👋 مرحباً! أنا مساعدك الأكاديمي. أرسل لي:</div>
        </div>
        <div class="message">
            <div class="bot-message" style="background: #35393e;">🔍 بحث: الذكاء الاصطناعي في الطب<br>✍️ صياغة: نصك هنا<br>📋 خطة علمية: عنوان بحثك</div>
        </div>
    </div>
    
    <div class="input-area">
        <input type="text" id="userInput" placeholder="اكتب رسالتك..." onkeypress="if(event.key==='Enter') sendMessage()">
        <button onclick="sendMessage()">إرسال</button>
    </div>
</div>

<script>
    const SERP_KEY = 'a9f941ff299e01fb953c5a292b3ec304fc4614a8eeda39aa453f638f1abc4bc9';
    const OPENAI_KEY = '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';
    
    function addBotMessage(text) {
        let chat = document.getElementById('chatBox');
        chat.innerHTML += `<div class="message"><div class="bot-message">${text}</div></div>`;
        chat.scrollTop = chat.scrollHeight;
    }
    
    function addUserMessage(text) {
        let chat = document.getElementById('chatBox');
        chat.innerHTML += `<div class="message"><div class="user-message">${text}</div></div>`;
        chat.scrollTop = chat.scrollHeight;
    }
    
    async function sendMessage() {
        let input = document.getElementById('userInput');
        let text = input.value.trim();
        if (!text) return;
        
        addUserMessage(text);
        input.value = '';
        
        if (text.startsWith('بحث:')) {
            let query = text.replace('بحث:', '').trim();
            addBotMessage('🔎 جاري البحث في جوجل سكولار...');
            try {
                let res = await fetch(`https://serpapi.com/search?engine=google_scholar&q=${encodeURIComponent(query)}&api_key=${SERP_KEY}`);
                let data = await res.json();
                let html = '';
                if (data.organic_results) {
                    data.organic_results.slice(0, 3).forEach(item => {
                        html += `<div class="result-item">
                            <strong>${item.title || ''}</strong><br>
                            <span style="color:#a0c4ff;">${item.publication_info?.summary || ''}</span><br>
                            <a href="${item.link}" target="_blank">🔗 رابط</a>
                            <small style="display:block; color:#aaa;">📌 اقتباسات: ${item.inline_links?.cited_by?.total || 0}</small>
                        </div>`;
                    });
                } else {
                    html = '❌ لا توجد نتائج';
                }
                addBotMessage(html);
            } catch {
                addBotMessage('⚠️ خطأ في الاتصال');
            }
        }
        else if (text.startsWith('صياغة:')) {
            let txt = text.replace('صياغة:', '').trim();
            addBotMessage('⏳ جاري إعادة الصياغة...');
            try {
                let res = await fetch('https://api.openai.com/v1/chat/completions', {
                    method: 'POST',
                    headers: {'Content-Type': 'application/json', 'Authorization': `Bearer ${OPENAI_KEY}`},
                    body: JSON.stringify({model: 'gpt-3.5-turbo', messages: [{role: 'user', content: 'أعد صياغة هذا النص بأسلوب أكاديمي: ' + txt}]})
                });
                let data = await res.json();
                addBotMessage(data.choices?.[0]?.message?.content || '❌ فشل');
            } catch {
                addBotMessage('⚠️ خطأ في الاتصال');
            }
        }
        else if (text.startsWith('خطة علمية:')) {
            let topic = text.replace('خطة علمية:', '').trim();
            addBotMessage('⏳ جاري كتابة خطة البحث العلمي...');
            try {
                let res = await fetch('https://api.openai.com/v1/chat/completions', {
                    method: 'POST',
                    headers: {'Content-Type': 'application/json', 'Authorization': `Bearer ${OPENAI_KEY}`},
                    body: JSON.stringify({model: 'gpt-3.5-turbo', messages: [{role: 'user', content: 'اكتب خطة بحث علمي مفصلة حول: ' + topic}]})
                });
                let data = await res.json();
                addBotMessage(data.choices?.[0]?.message?.content.replace(/\n/g,'<br>') || '❌ فشل');
            } catch {
                addBotMessage('⚠️ خطأ في الاتصال');
            }
        }
        else if (text.startsWith('خطة أدبية:')) {
            let topic = text.replace('خطة أدبية:', '').trim();
            addBotMessage('⏳ جاري كتابة خطة البحث الأدبي...');
            try {
                let res = await fetch('https://api.openai.com/v1/chat/completions', {
                    method: 'POST',
                    headers: {'Content-Type': 'application/json', 'Authorization': `Bearer ${OPENAI_KEY}`},
                    body: JSON.stringify({model: 'gpt-3.5-turbo', messages: [{role: 'user', content: 'اكتب خطة بحث أدبي مفصلة حول: ' + topic}]})
                });
                let data = await res.json();
                addBotMessage(data.choices?.[0]?.message?.content.replace(/\n/g,'<br>') || '❌ فشل');
            } catch {
                addBotMessage('⚠️ خطأ في الاتصال');
            }
        }
        else {
            addBotMessage('❓ أرسل:\n🔍 بحث: ...\n✍️ صياغة: ...\n📋 خطة علمية/أدبية: ...');
        }
    }
</script>
</body>
</html>
