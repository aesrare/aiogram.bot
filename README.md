# aiogram.bot

#подключаем необходимые библиотеки

from aiogram import Bot, Dispatcher, executor, types
from random import choice
from aiogram.types.web_app_info import WebAppInfo
from aiogram.contrib.fsm_storage.memory import MemoryStorage
import asyncio
import logging

bot = Bot('api')
dp = Dispatcher(bot)
CREATOR_ID = 'id'
storage = MemoryStorage()

#ПРИВЕТСТВИЕ

@dp.message_handler(commands=['start'])
async def start(message: types.Message):
    await message.answer_sticker('stiker_id')
    markup = types.ReplyKeyboardMarkup()
    btn1 = types.KeyboardButton('/help')
    btn2 = types.KeyboardButton('/help', callback_data='Тут будет перечень доступных команд и он будет пополняться по мере доработки бота. Пока можешь потестить следующие: /personal /image /random')
    markup.row(btn1)
    await message.reply(f'Здравствуй, {message.from_user.first_name}', reply_markup=markup)
   # await message.reply("Тут будет перечень доступных команд и он будет пополняться по мере доработки бота.\n\tПока можешь потестить следующие:\n\t/personal\n\t/image\n\t/random")

@dp.callback_query_handler()
async def callback(call):
    await call.message.answer(call.data)

#ПЕРЧЕНЬ

@dp.message_handler(commands=['help'])
async def help(message: types.Message):
    markup = types.ReplyKeyboardMarkup()
    btn6 = types.KeyboardButton('/random')
    btn7 = types.KeyboardButton('/image')
    btn8 = types.KeyboardButton('/personal')
    markup.row(btn6, btn7, btn8)
    await message.reply("Тут будет перечень доступных команд и он будет пополняться по мере доработки бота.\n\tПока можешь потестить следующие:\n\t/personal\n\t/image\n\t/random")

#СПИСКИ

RANDOM_TASKS = ['список задач', '']

LYRIC = ['', '']

IMAGES = ['photo.jpg', '']

#ССЫЛКИ НА СОЦ СЕТИ
@dp.message_handler(commands=['personal'])
async def personal(message):
    markup = types.InlineKeyboardMarkup()
    btn1 = types.InlineKeyboardButton('instagram', web_app=WebAppInfo(url='https://'))
    btn2 = types.InlineKeyboardButton('telegram', web_app=WebAppInfo(url='https://'))
    markup.row(btn1, btn2)
    btn3 = types.InlineKeyboardButton('spotify playlist', web_app=WebAppInfo(url='https://'))
    btn4 = types.InlineKeyboardButton('yandex music playlist', web_app=WebAppInfo(url='https://'))
    markup.row(btn3, btn4)
    btn5 = types.InlineKeyboardButton('tg channel', web_app=WebAppInfo(url='https:/'))
    markup.row(btn5)
    await message.reply('Познакомимся?', reply_markup=markup)

#РАНДОМНЫЕ ЗАДАНИЯ

@dp.message_handler(commands=["random"])
async def random(message):
    task = choice(RANDOM_TASKS)
    await message.reply(f'{task}')

#РАНДОМНЫЕ КАРТИНКИ

@dp.message_handler(commands=['image'])
async def send_random_image(message):
    # Отправляем случайное изображение из списка в указанный чат
    image = choice(IMAGES)
    await message.reply(f'{image}')

#ОТПРАВКА СООБЩЕНИЙ В ЧАТ С СОЗДАТЕЛЕМ

async def forward_to_creator(message: types.Message):
    await bot.send_message(chat_id=CREATOR_ID, text=message.text)


# Обработка всех входящих сообщений
@dp.message_handler()
async def echo(message: types.Message):
# Пересылаем сообщение в личный чат с создателем бота
    await forward_to_creator(message)



executor.start_polling(dp)
