import telebot
import requests
import os

BOT_TOKEN = os.environ.get("BOT_TOKEN")
OMDB_API_KEY = os.environ.get("OMDB_API_KEY")

bot = telebot.TeleBot(BOT_TOKEN)

@bot.message_handler(commands=['start'])
def welcome(message):
    bot.send_message(message.chat.id, "🎬 Salom! Qaysi kinoni qidirmoqchisiz? Kino nomini yozing.")

@bot.message_handler(func=lambda message: True)
def search_movie(message):
    query = message.text
    url = f"http://www.omdbapi.com/?apikey={OMDB_API_KEY}&t={query}"
    response = requests.get(url).json()

    if response["Response"] == "True":
        msg = f"""📽️ <b>{response["Title"]}</b> ({response["Year"]})
⭐ IMDB: {response["imdbRating"]}
🎭 Janr: {response["Genre"]}
📝 {response["Plot"]}"""
        bot.send_photo(message.chat.id, response["Poster"], caption=msg, parse_mode='HTML')
    else:
        bot.send_message(message.chat.id, "❌ Kino topilmadi. Boshqa nom kiriting.")

bot.polling()
