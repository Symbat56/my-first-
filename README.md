import os
import asyncio
from aiogram import Bot, Dispatcher
from aiogram.types import Message
from aiogram.dispatcher.filters import CommandStart, Command
from bitcoinrpc.authproxy import AuthServiceProxy

rpc_user = os.getenv("rpc_user")
rpc_password = os.getenv("rpc_password", "")
rpc_url = os.getenv("rpc_url")
token = os.getenv("BITCOIN_BOT_TOKEN")

bot = Bot(token)
dp = Dispatcher(bot)

@dp.message_handler(CommandStart())
async def start(message: Message):
    await message.answer("/getnewaddress - for new address\n/getbalance - for balance")

@dp.message_handler(Command("getnewaddress"))
async def get_new_address(message: Message):
    rpc_connection = AuthServiceProxy(f"http://{rpc_user}:{rpc_password}@{rpc_url}")
    res = rpc_connection.getnewaddress()
    await message.answer(str(res))

@dp.message_handler(Command("getbalance"))
async def get_balance(message: Message):
    rpc_connection = AuthServiceProxy(f"http://{rpc_user}:{rpc_password}@{rpc_url}")
    res = rpc_connection.getbalance()
    await message.answer(str(res))

async def main():
    await dp.start_polling()

if __name__ == "__main__":
    asyncio.run(main())
