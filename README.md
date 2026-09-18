# API-ni-deploy-ga-tayyor-qilish-dotenv-health-skriptlar
. Kerakli paketni o'rnatish
Terminalda quyidagi buyruqni bajarib dotenv paketini o'rnating:

Bash
npm install dotenv
2. .env faylini yaratish
Loyihaning ildiz papkasida (root directory) .env faylini yarating va quyidagi maxfiy sozlamalarni kiriting:

Фрагмент кода
PORT=3000
DATABASE_URL=postgresql://user:parol@localhost:5432/db_nomi
JWT_SECRET=sizning_juda_maxfiy_jwt_kalitingiz
CORS_ORIGINS=http://localhost:3000,https://sizning-domen.uz
3. .gitignore ga qo'shish
Maxfiy ma'lumotlar GitHub'ga tushib ketmasligi uchun .gitignore fayliga .env ni yozib qo'ying:

Plaintext
node_modules/
.env
.DS_Store
4. package.json skriptlarini sozlash
package.json faylidagi scripts qismini quyidagicha o'zgartiring:

JSON
"scripts": {
  "dev": "nodemon server.js",
  "start": "node server.js"
}
5. server.js faylini sozlash (To'liq namuna)
dotenv kutubxonasi eng birinchi qatorda chaqirilishi va barcha maxfiy ma'lumotlar process.env orqali olinishi shart:

JavaScript
// 1. Eng birinchi qatorda dotenv'ni ulash (hamma narsadan oldin!)
require('dotenv').config();

const express = require('express');
const cors = require('cors');

const app = express();

// Middleware'lar
app.use(cors({
  origin: process.env.CORS_ORIGINS ? process.env.CORS_ORIGINS.split(',') : '*',
  credentials: true
}));
app.use(express.json());

// 2. Health-check endpoint (Deploy tizimlari uchun server holatini tekshirish)
app.get('/health', (req, res) => {
  res.status(200).json({ 
    status: 'ok', 
    vaqt: new Date().toISOString() 
  });
});

// Misol uchun asosiy route
app.get('/api/test', (req, res) => {
  res.json({ message: 'API muvaffaqiyatli ishlamoqda!' });
});

// 3. Portni process.env.PORT dan o'qish (fallback bilan 3000)
const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
  console.log(`Server ${PORT}-portda ishga tushdi`);
});
Qisqacha eslatmalar:
npm run dev: Ishlab chiqish (development) jarayonida nodemon yordamida avtomatik yanglanib turishi uchun ishlatiladi.

npm start: Production serverida (masalan, Render, Heroku yoki VPS'da) dasturni to'g'ridan-to'g'ri ishga tushirish uchun ishlatiladi.

/health: Cloud platformalar (Render, Docker, Kubernetes va h.k.) server tirik yoki tirik emasligini tekshirish uchun ushbu endpoint'ga so'rov yuborib turadi.
