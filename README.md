
# 5274421302:AAGXBBeX7vQbJHxN3q_aTcTrrYHfYXmM5JE

import telebot
from telebot import types
#	bot.reply_to(message, message.text)

name = ' '
surname = ' '
age = 0

bot = telebot.TeleBot("5274421302:AAGXBBeX7vQbJHxN3q_aTcTrrYHfYXmM5JE")

@bot.message_handler(commands=['start', 'help'])
def send_welcome(message):
	bot.reply_to(message, "Привет. Для начала работы напиши - старт")

@bot.message_handler(func=lambda m: True)
def echo_all(message):
    if message.text == 'старт':
        bot.reply_to(message, 'Для прохождения тестов зарегистрируйтесь с помощью функции - /reg')
    elif message.text == '/reg':
        bot.send_message(message.from_user.id, "Привет! Давай познакомимся! Как тебя зовут?")
        bot.register_next_step_handler(message, reg_name)

	#bot.reply_to(message, message.text)
def reg_name(message):
    global name
    name = message.text
    bot.send_message(message.from_user.id, "Какая у вас фамилия?")
    bot.register_next_step_handler(message, reg_surname)

def reg_surname(message):
    global surname
    surname = message.text
    bot.send_message(message.from_user.id, "Сколько вам лет?")
    bot.register_next_step_handler(message, reg_age)

def reg_age(message):
    global age
    #age = message.text
    while age == 0:
        try:
            age = int(message.text)
        except Exception:
            bot.send_message(message.from_user.id, "Вводите цифрами!")

    keyboard = types.InlineKeyboardMarkup()
    key_yes = types.InlineKeyboardButton(text='Да', callback_data='yes')
    keyboard.add(key_yes)
    key_no = types.InlineKeyboardButton(text='Нет', callback_data='no')
    keyboard.add(key_no)
    question = 'Тебе ' + str(age) + ' лет? И тебя зовут: ' + name + ' ' + surname + '?'
    bot.send_message(message.from_user.id, text = question, reply_markup=keyboard)

@bot.callback_query_handler(func=lambda call: True)
def callback_worker(call):
    if call.data == "yes":
        bot.send_message(call.message.chat.id, "Приятно познакомиться! Теперь можно приступать к работе.")
    elif call.data == "no":
        bot.send_message(call.message.chat.id, "Error.Try again")
        bot.send_message(call.message.chat.id, "Привет! Давай познакомимся! Как тебя зовут?")
        bot.register_next_step_handler(call.message, reg_name)



bot.infinity_polling()
