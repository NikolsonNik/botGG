import telebot
import webbrowser
from telebot import types
from telebot.types import Message, CallbackQuery

# Создаем экземпляр бота
API_TOKEN = '7699217542:AAHANxfrtdfrrBTPifGcEz2uMR1cvKbsqrY'
TARGET_CHAT_ID = '-4593414510'

bot = telebot.TeleBot(API_TOKEN)


# Состояния пользователя
class States:
    NAME = 'name'
    DATE = 'date'
    TIME = 'time'
    PEOPLE = 'people'
    CONTACT = 'contact'


user_data = {}

def create_start_markup():
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True)
    start_button = types.KeyboardButton("Старт")
    markup.add(start_button)
    return markup

@bot.message_handler(commands=['start'])
def send_welcome(message: Message):
    bot.send_message(message.chat.id, "Добро пожаловать! Как вас зовут?")
    bot.register_next_step_handler(message, get_name)


def get_name(message: Message):
    user_id = message.from_user.id
    user_data[user_id] = {States.NAME: message.text}
    bot.send_message(message.chat.id, "Укажите дату бронирования (в удобной форме):")
    bot.register_next_step_handler(message, get_date)


def get_date(message: Message):
    user_id = message.from_user.id
    user_data[user_id][States.DATE] = message.text
    bot.send_message(message.chat.id, "Укажите время бронирования :")
    bot.register_next_step_handler(message, get_time)


def get_time(message: Message):
    user_id = message.from_user.id
    user_data[user_id][States.TIME] = message.text
    bot.send_message(message.chat.id, "Укажите количество человек:(при бронировании от 6 человек чаевые будут включены в счёт)")
    bot.register_next_step_handler(message, get_people)


def get_people(message: Message):
    user_id = message.from_user.id
    user_data[user_id][States.PEOPLE] = message.text
    bot.send_message(message.chat.id, "Укажите ваш номер телефона:")
    bot.register_next_step_handler(message, get_contact)


def get_contact(message: Message):
    user_id = message.from_user.id
    user_data[user_id][States.CONTACT] = message.text
    send_booking_info(user_id, message.chat.id)


def send_booking_info(user_id, chat_id):
    name = user_data[user_id][States.NAME]
    date = user_data[user_id][States.DATE]
    time = user_data[user_id][States.TIME]
    people = user_data[user_id][States.PEOPLE]
    contact = user_data[user_id][States.CONTACT]

    booking_info = (
        f"Бронирование столика:\n"
        f"Имя: {name}\n"
        f"Дата: {date}\n"
        f"Время: {time}\n"
        f"Количество человек: {people}\n"
        f"Контактный номер: {contact}"
    )

    bot.send_message(chat_id, "Спасибо за бронирование!")
    bot.send_message(TARGET_CHAT_ID, booking_info)

@bot.message_handler(commands=['site', 'vk'])
def site(message):
    webbrowser.open('https://vk.com/guggenheimbar')





bot.polling(none_stop=True)
