import asyncio
import logging
from pyrogram import Client, filters
from pytgcalls import PyTgCalls
from pytgcalls.types import AudioPiped, Update

# إعداد السجلات (لتعرف حالة البوت في Koyeb)
logging.basicConfig(level=logging.INFO)

# --- بياناتك الخاصة ---
API_ID = 26569766
API_HASH = "80577908c69707925150824b6f178229"
SESSION = "BAH8RhoAS-udGn6zNr8N-hYmyBHzbpWpCqc7W-XZjnCCUzo0yx4JMeEm0nctXdk5y-NP_OkhPH-h34QA1ZwI89twcYHA6UjpqgmRafsjfgJCOHpM_XpkaMnDm8nbAV_q8RlmXes1wfxeXFTDs7jssmtYOuMY0hAmAFAFv81zSXtEEhoz7OOm3indPgkBIBqwbDJCgF5Sb2oK-0G2gLMsVRgeg4pHDemhSWd3tWOzJUOVev5JXyVVDJgztBUrUoQnA29nJT5rJwmJLIq3LtL3_rwlm_iagywAblDxWGM9zkAdoYoQWsk_KaX-17LKClCnvWiaUb-O04F2bYR6R7L2-w69vTcCfwAAAAHqFtoRAA"
CHAT_ID = -1006476163398  # آيدي القناة
STREAM_URL = "https://quraan.us.rdp.sh/8282/stream" # رابط إذاعة القرآن الكريم

app = Client("QuranStream", api_id=API_ID, api_hash=API_HASH, session_string=SESSION)
call_py = PyTgCalls(app)

async def start_stream():
    try:
        await call_py.join_group_call(
            CHAT_ID,
            AudioPiped(STREAM_URL)
        )
        logging.info("✅ تم بدء البث بنجاح!")
        # رسالة تأكيدية في القناة عند التشغيل
        await app.send_message(CHAT_ID, "✨ تم بدء البث المباشر للقرآن الكريم بنجاح.\nرمضان مبارك عليكم.")
    except Exception as e:
        logging.error(f"❌ فشل بدء البث: {e}")

@call_py.on_update()
async def handle_updates(client, update: Update):
    # إعادة التشغيل التلقائي إذا انقطع البث
    if isinstance(update, Update) and update.action == "closed":
        logging.info("⚠️ انقطع البث، جاري إعادة التشغيل...")
        await asyncio.sleep(5)
        await start_stream()

async def main():
    await app.start()
    logging.info("🚀 الحساب شغال الآن...")
    await call_py.start()
    await start_stream()
    await asyncio.Idle()

if __name__ == "__main__":
    asyncio.get_event_loop().run_until_complete(main())
