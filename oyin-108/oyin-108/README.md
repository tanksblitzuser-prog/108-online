# 108 — onlayn karta o'yini

3–5 o'yinchi, xona kodi orqali, telefon va kompyuterda. Hamma qoidalar **serverda** hal qilinadi,
har bir o'yinchi faqat o'z kartalarini ko'radi. Tashqi kutubxona (npm paket) kerak emas — faqat Node.js.

## Fayllar

| Fayl | Vazifasi |
|---|---|
| `server.js` | Server: xonalar, navbat, taymer, qayta ulanish, botlar |
| `wslite.js` | Kutubxonasiz WebSocket (server uchun) |
| `engine.js` | O'yin qoidalari (server ishlatadi) |
| `public/index.html` | O'yin sahifasi (bosh sahifa, lobbi, stol) |
| `test.js`, `test_server.js` | Avtomatik testlar |
| `render.yaml` | Render.com uchun sozlama |

## 1. Kompyuteringizda sinab ko'rish

1. Node.js o'rnating (nodejs.org, 18 yoki yangiroq).
2. Papkada terminal oching va yozing: `node server.js`
3. Brauzerda oching: **http://localhost:3000**
4. Xona yarating, kodni oling. Boshqa oynada (yoki telefonda) o'sha kod bilan kiring.

Bitta Wi-Fi'dagi do'stlar bilan: kompyuteringizning IP manzilini bilib (`ipconfig` / `ifconfig`),
ular `http://SIZNING-IP:3000` ni ochadi.

## 2. Internetga joylash (do'stlar istalgan joydan kirishi uchun)

**Netlify va Vercel bu o'yin uchun yaramaydi**, chunki ularda doimiy ishlaydigan WebSocket serveri bo'lmaydi.
Node.js serverini ishlatadigan xosting kerak. Eng oddiy yo'li — **Render.com**:

1. GitHub'da yangi repozitoriya oching va shu papkadagi fayllarni (jumladan `public/` ni) yuklang.
2. render.com'da ro'yxatdan o'ting → **New → Web Service** → GitHub repozitoriyangizni tanlang.
3. Sozlamalar: **Runtime: Node**, **Build Command: `npm install`**, **Start Command: `node server.js`**, **Instance type: Free**.
   (Yoki **New → Blueprint** tanlasangiz, `render.yaml` hamma narsani o'zi to'ldiradi.)
4. **Create** bosing. 1–2 daqiqadan keyin `https://nomingiz.onrender.com` manzili chiqadi.
5. Shu havolani do'stlarga yuboring. Yoki o'yin ichidagi **"Havolani nusxalash"** tugmasi bilan xona havolasini yuboring.

Eslatmalar:
- Bepul tarifda server ~15 daqiqa hech kim kirmasa uxlab qoladi, keyingi ochilish 30–60 soniya oladi.
  O'yin oldidan sahifani oldindan oching. Uxlab qolmasligi uchun `/healthz` manzilini har 5 daqiqada
  tekshirib turuvchi bepul xizmat (masalan UptimeRobot) ulash mumkin.
- Bepul tarif shartlari o'zgarib turadi, Render sahifasida tekshiring.
- Boshqa variantlar: Railway, Fly.io yoki o'z VPS'ingiz (`node server.js`, oldida nginx bo'lsa WebSocket uchun
  `Upgrade` va `Connection` sarlavhalarini o'tkazing).

## 3. Sozlamalar (ixtiyoriy, muhit o'zgaruvchilari)

| O'zgaruvchi | Standart | Ma'nosi |
|---|---|---|
| `PORT` | 3000 | Server porti (Render o'zi beradi) |
| `TURN_MS` | 40000 | Bitta yurish uchun vaqt (ms) |
| `SHUFFLE_MS` | 5000 | Aralashtirish tanaffusi (ms) |
| `MAX_ROOMS` | 300 | Bir vaqtdagi eng ko'p xona |
| `ALLOWED_ORIGIN` | — | Faqat shu saytdan ulanishga ruxsat, masalan `https://nomingiz.onrender.com` |

## 4. Qanday ishlaydi

- **Xona:** 5 belgili kod (0/O, 1/I/L aralashmaydi). Faqat lobbida qo'shilish mumkin.
- **Maxfiylik:** server har o'yinchiga faqat uning qo'lini va boshqalarning karta sonini yuboradi.
- **Navbat taymeri:** 40 soniya. Kechiksangiz, server o'rningizga yuradi; ketma-ket 2 marta kechiksangiz avto-rejim
  (yurish qilsangiz, qaytadan o'zingiz boshqarasiz).
- **Uzilish:** sahifani yangilash yoki internet uzilishi o'yindan chiqarmaydi. Qayta kirsangiz o'sha joyga qaytasiz
  (brauzer xotirasidagi maxfiy token orqali). Uzilgan paytda navbatingiz kelsa, bot yuradi.
- **Botlar:** host lobbida bot qo'sha oladi. "Botlar bilan mashq" tugmasi yolg'iz o'ynash uchun.
- **Chiqish:** o'yin paytida chiqib ketsangiz, o'rningizga bot o'ynaydi.
- **Qayta o'ynash:** o'yin tugagach host "Qayta o'ynash"ni bosadi.

## 5. Testlar

```
node test.js            # qoidalar mexanizmi (17 ta test)
node test_server.js     # server (17 ta test; Node 22+ kerak)
```

## 6. Cheklovlar

- Xonalar server xotirasida turadi: server qayta ishga tushsa, faol xonalar yo'qoladi.
- Bitta server nusxasi uchun mo'ljallangan (kichik kompaniyalar uchun yetarli).
- Chat yo'q, faqat 6 ta emoji-reaksiya bor.
