import discord
import os
from dotenv import load_dotenv
from webserver import keep_alive
from discord.ext import commands

# Загружаем переменные окружения из .env файла
load_dotenv()

# Настройка intents
intents = discord.Intents.default()
intents.members = True  # Для отслеживания событий, связанных с участниками

# Создаем экземпляр бота
bot = commands.Bot(command_prefix='!', intents=intents)

@bot.event
async def on_ready():
    print(f'We have logged in as {bot.user}')

@bot.event
async def on_member_update(before, after):
    # Проверяем, если роль "верефнутый" была добавлена участнику
    role_id = 1325918814101180476  # ID роли "верефнутый"
    verefnyt_role = discord.utils.get(after.roles, id=role_id)

    if verefnyt_role and verefnyt_role not in before.roles:
        try:
            await after.send(
                """Поздравляем с успешной верификацией и официальным присоединением к нашему сообществу!

📚 Важная информация:
Для быстрого ознакомления с рабочими процессами и получения необходимых знаний настоятельно рекомендуется посетить канал <#1325913319449559174>. В этом разделе содержатся все ключевые руководства, ресурсы и информация, необходимые для эффективного выполнения вашей работы.

После того как вы ознакомитесь со всеми материалами и скачаете необходимое программное обеспечение, обязательно сообщите об этом куратору для подтверждения вашей готовности к работе."""
            )
        except Exception as e:
            print(f"Не удалось отправить сообщение: {e}")

# Запускаем Flask-сервер для UptimeRobot
keep_alive()

# Запускаем бота
TOKEN = os.getenv('DISCORD_TOKEN')  # Теперь токен загружается из переменной окружения DISCORD_TOKEN
if TOKEN:
    bot.run(TOKEN)
else:
    print("Токен не найден! Убедитесь, что вы добавили его в .env файл.")
