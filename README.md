import requests

# ====== НАСТРОЙКИ CONTRA-SENTIMENT AI ======
# Вставь свой секретный ключ из BotFather вместо текста ниже (кавычки оставь!):
TELEGRAM_TOKEN = "8991858267:AAFvWFDYxg-dn9ZJ534aIeXezG8qXZ2dXFw"
CHAT_ID = "6181831767"

# Твоя квантовая матрица торговых коридоров
THRESHOLD_LOW = 30   # Паника толпы (выкупаем лои)
THRESHOLD_HIGH = 60  # Перегретая жадность (фиксируем ROI)

MESSAGE_LOW = "Рынок в экстремальной панике! Историческая точка твоей прибыли. Пора покупать лои."
MESSAGE_HIGH = "Рынок перегрет! Капитал в безопасности, фиксируем ROI и уходим в кэш."

FNG_URL = "https://alternative.me"

def get_fear_greed():
    response = requests.get(FNG_URL, timeout=10)
    response.raise_for_status()
    data = response.json()
    return int(data["data"][0]["value"])

def send_telegram(text):
    url = f"https://telegram.org{TELEGRAM_TOKEN}/sendMessage"
    requests.post(url, data={"chat_id": CHAT_ID, "text": text}, timeout=10)

def main():
    try:
        value = get_fear_greed()
        print(f"Текущий индекс страха и жадности: {value}")

        # Сигнал 1: Зона паники толпы (индекс <= 30)
        if value <= THRESHOLD_LOW:
            send_telegram(f"🚨 {MESSAGE_LOW}\n(Индекс: {value})")
            print("Сигнал отправлен: Зона паники.")
        
        # Сигнал 2: Зона экстремальной жадности (индекс >= 60)
        elif value >= THRESHOLD_HIGH:
            send_telegram(f"💰 {MESSAGE_HIGH}\n(Индекс: {value})")
            print("Сигнал отправлен: Зона жадности.")
        
        # Нейтральная зона (от 31 до 59) — робот хладнокровно молчит
        else:
            print("Рынок в нейтральной зоне. Бот хладнокровно молчит.")

    except Exception as error:
        print("Ошибка работы скрипта:", error)

if __name__ == "__main__":
    main()
