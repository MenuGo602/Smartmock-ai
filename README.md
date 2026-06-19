# Grading moduli — backendga ulash

Bu papka (`app/grading/`) sizning mavjud FastAPI loyihangizga (backend/app/) shunchaki
ko'chirib qo'yiladigan tayyor modul. Anthropic (Claude) emas, **OpenAI** orqali baholaydi.

## 1. Fayllarni joylashtirish

`app/grading/` papkasini to'liq sizning `backend/app/` ichiga ko'chiring (auth, exams,
admin routerlaringiz qaysi joyda bo'lsa, shu yerga, yonma-yon).

## 2. requirements.txt ga qo'shing

```
openai>=1.40.0
```

## 3. main.py ga routerni ulang

```python
from app.grading.router import router as grading_router

app.include_router(grading_router, prefix="/api/v1")
```

(prefiks — sizning boshqa routerlaringiz qanday ulanganiga qarab moslang, masalan
agar auth/exams `/api/v1` prefiksi bilan ulangan bo'lsa, xuddi shunday qiling —
frontenddagi `VITE_API_URL` shu prefiksgacha bo'lgan manzilni ko'rsatishi kerak.)

## 4. Auth bilan ulash (muhim!)

Hozircha `/grading/writing` va `/grading/speaking` endpoint'lari **ochiq** — har kim
chaqira oladi va bu OpenAI hisobingizdan pul sarflaydi. `router.py` ichidagi TODO
joyga sizning mavjud Telegram auth dependency'ingizni qo'shing, masalan:

```python
from app.auth import get_current_telegram_user

@router.post("/writing", response_model=WritingGradeResponse)
async def grade_writing_endpoint(
    payload: WritingGradeRequest,
    user = Depends(get_current_telegram_user),
):
    ...
```

## 5. Environment o'zgaruvchilari

`.env` (yoki Railway > Service > Variables) ga qo'shing:

```
OPENAI_API_KEY=sk-...
OPENAI_GRADING_MODEL=gpt-4o-mini   # ixtiyoriy, default shu
```

## Eslatma: javob formati

Eski versiyada Claude'dan kelgan matnni qo'lda backtick'lardan tozalab JSON qilib
parse qilingan edi. Bu yerda OpenAI'ning `response_format={"type": "json_object"}`
("JSON mode") xususiyatidan foydalanilgan — model 100% valid JSON qaytarishga
majburlanadi, shuning uchun parse xatoligi ehtimoli deyarli yo'q.
