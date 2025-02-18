<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Welcome to My Web App</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #f3f4f6;
            font-family: Arial, sans-serif;
        }
        .container {
            padding: 20px;
            background: white;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba (0, 0, 0, 0.1);
            text-align: center;
        }
        h1 {
            color: #333;
        }
        p {
            color: #666;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Welcome to My Web App</h1>
        <p>This is a simple static web app you can host anywhere.</p>
    </div>
</body>
</html>
from telegram import Update
from telegram.ext import Updater, CommandHandler, CallbackContext

TOKEN = "YOUR_BOT_TOKEN"

def start(update: Update, context: CallbackContext):
    update.message.reply_text("Hello! I'm your bot.")

def help_command(update: Update, context: CallbackContext):
    update.message.reply_text("Here are the available commands:\n/start - Start bot\n/help - Show help")

updater = Updater(TOKEN, use_context=True)
dispatcher = updater.dispatcher

dispatcher.add_handler(CommandHandler("start", start))
dispatcher.add_handler(CommandHandler("help", help_command))

updater.start_polling()
updater.idle()
from flask import Flask, request
import telegram
import requests
import datetime
import random

TOKEN = "YOUR_BOT_TOKEN"
bot = telegram.Bot(token=TOKEN)

app = Flask(__name__)

# List of jokes and quotes
jokes = [
    "Why don’t skeletons fight each other? They don’t have the guts.",
    "What do you call fake spaghetti? An impasta!",
    "I told my wife she was drawing her eyebrows too high. She looked surprised."
]
quotes = [
    "Believe in yourself!",
    "You are stronger than you think.",
    "Never stop learning, because life never stops teaching."
]

@app.route('/webhook', methods=['POST'])
def webhook():
    update = telegram.Update.de_json(request.get_json(force=True), bot)
    chat_id = update.message.chat.id
    text = update.message.text

    if text == "/start":
        bot.sendMessage(chat_id=chat_id, text="Hello! I'm your bot. Use /help to see commands.")
    elif text == "/help":
        bot.sendMessage(chat_id=chat_id, text="Available commands:\n/start - Start bot\n/help - Show help\n/info - Bot info\n/weather city - Get weather\n/time - Current time\n/joke - Tell a joke\n/quote - Get a quote")
    elif text == "/info":
        bot.sendMessage(chat_id=chat_id, text="I'm a simple bot built with Python. Enjoy!")
    elif text.startswith("/echo "):
        bot.sendMessage(chat_id=chat_id, text=text[6:])  # Reply with the same text
    elif text.startswith("/weather "):
        city = text.split("/weather ", 1)[1]
        weather_info = get_weather(city)
        bot.sendMessage(chat_id=chat_id, text=weather_info)
    elif text == "/time":
        now = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        bot.sendMessage(chat_id=chat_id, text=f"Current time: {now}")
    elif text == "/joke":
        bot.sendMessage(chat_id=chat_id, text=random.choice(jokes))
    elif text == "/quote":
        bot.sendMessage(chat_id=chat_id, text=random.choice(quotes))
    else:
        bot.sendMessage(chat_id=chat_id, text="I don't recognize that command. Use /help to see available commands.")

    return "OK", 200

# Function to get weather (Replace with actual API key)
def get_weather(city):
    API_KEY = "YOUR_OPENWEATHERMAP_API_KEY"
    URL = f"https://api.openweathermap.org/data/2.5/weather?q={city}&appid={API_KEY}&units=metric"
    
    response = requests.get(URL)
    if response.status_code == 200:
        data = response.json()
        weather = f"Weather in {city}:\nTemperature: {data['main']['temp']}°C\nCondition: {data['weather'][0]['description'].capitalize()}"
        return weather
    else:
        return "City not found. Please try again."

if __name__ == '__main__':
    app.run(port=5000)


# Bobo
