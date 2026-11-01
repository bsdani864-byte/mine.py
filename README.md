# mine.py
import os
import sys

import requests

TOKEN = os.environ.get("BOT_TOKEN")
CHAT_ID = os.environ.get("CHAT_ID")  # необязательно: ваш chat_id для тестового сообщения

API_URL = f"https://api.telegram.org/bot{TOKEN}"


def check_bot() -> bool:
    try:
        response = requests.get(f"{API_URL}/getMe", timeout=10)
        data = response.json()
    except requests.RequestException as e:
        print(f"❌ Ошибка соединения: {e}")
        return False

    if not data.get("ok"):
        print(f"❌ Бот не работает: {data.get('description')}")
        return False

    bot = data["result"]
    print("✅ Бот работает!")
    print(f"   Имя:      {bot['first_name']}")
    print(f"   Username: @{bot['username']}")
    print(f"   ID:       {bot['id']}")
    return True


def send_test_message() -> None:
    if not CHAT_ID:
        return

    response = requests.post(
        f"{API_URL}/sendMessage",
        json={"chat_id": CHAT_ID, "text": "Проверка работоспособности ✅"},
        timeout=10,
    )
    data = response.json()

    if data.get("ok"):
        print("✅ Тестовое сообщение отправлено")
    else:
        print(f"❌ Не удалось отправить сообщение: {data.get('description')}")


if __name__ == "__main__":
    if not check_bot():
        sys.exit(1)
    send_test_message()
