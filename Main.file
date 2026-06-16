import os
import requests
import time
import random
from datetime import datetime

# ==================== AYARLAR (VARIABLES) ====================
# Bu değerleri Railway panelindeki "Variables" kısmından yöneteceğiz.
API_URL = os.getenv("API_URL", "BURAYA_YAKALADIGIN_URL_GELECEK")
TELEGRAM_TOKEN = os.getenv("TELEGRAM_TOKEN", "BURAYA_BOT_TOKEN_GELECEK")
CHAT_ID = os.getenv("CHAT_ID", "BURAYA_CHAT_ID_GELECEK")
# ==========================================================

HEADERS = {
    "User-Agent": "Mozilla/5.0 (iPhone; CPU iPhone OS 16_6 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/16.6 Mobile/15E148 Safari/604.1",
    "Accept": "application/json",
}

def telegram_mesaj_gonder(mesaj):
    """Anomali sinyalini doğrudan Telegram'a fırlatır."""
    if "GELECEK" in TELEGRAM_TOKEN or "GELECEK" in CHAT_ID:
        print("⚠️ Telegram ayarları henüz yapılmadı.")
        return
        
    url = f"https://api.telegram.org/bot{TELEGRAM_TOKEN}/sendMessage"
    payload = {
        "chat_id": CHAT_ID,
        "text": mesaj,
        "parse_mode": "Markdown"
    }
    try:
        requests.post(url, json=payload, timeout=10)
    except Exception as e:
        print(f"❌ Telegram gönderim hatası: {e}")

def bulteni_analiz_et():
    simdiki_zaman = datetime.now().strftime('%H:%M:%S')
    print(f"🔄 [{simdiki_zaman}] Bülten anomaliler için taranıyor...")
    
    try:
        response = requests.get(API_URL, headers=HEADERS, timeout=15)
        if response.status_code == 200:
            veri = response.json()
            maclar = veri.get("matches", veri.get("data", veri.get("bulten", [])))
            
            if not maclar and isinstance(veri, list):
                maclar = veri
                
            for mac in maclar:
                mac_adi = mac.get("name", mac.get("matchName", "Bilinmeyen Maç"))
                lig_adi = mac.get("league", mac.get("leagueName", "Genel Lig"))
                mac_saati = mac.get("date", mac.get("matchTime", ""))
                
                # Nesine kuponundaki "1. Yarı / 2. Yarı KG Var" marketinin oranı
                oran = mac.get("odds", {}).get("iy_2y_kg_var", 0) 
                
                # 20.00+ ÜSTÜ ANOMALİ FİLTRESİ
                if oran >= 20.0:
                    sinyal_metni = (
                        f"🚨 *ANOMALİ SİNYALİ YAKALANDI!*\n\n"
                        f"🏆 *Lig:* {lig_adi}\n"
                        f"⚽ *Maç:* {mac_adi}\n"
                        f"⏰ *Saat:* {mac_saati}\n"
                        f"🔥 *Oran:* `{oran}`\n\n"
                        f"🎯 _1. Yarı / 2. Yarı KG - Evet/Evet senaryosu aktif!_"
                    )
                    print(f"🎯 Anomali Bulundu: {mac_adi} - Oran: {oran}")
                    telegram_mesaj_gonder(sinyal_metni)
        else:
            print(f"❌ Site hata kodu döndürdü: {response.status_code}")
    except Exception as e:
        print(f"⚠️ Tarama hatası: {e}")

if __name__ == "__main__":
    print("🚀 Gözcü Botu Aktif. Bulut taraması başlatıldı...")
    telegram_mesaj_gonder("🚀 *Gözcü Botu Başarıyla Başlatıldı!* Sistem 7/24 Railway bulutunda yayında.")
    
    while True:
        bulteni_analiz_et()
        # Radara yakalanmamak için 3-5 dk arası rastgele bekleme
        time.sleep(random.randint(180, 300))
