# Lotshot Smart Contracts

Репозиторий содержит смарт‑контракты на FunC и скрипты для запуска лотереи Lotshot в сети TON.

## Установка
1. `npm install`
2. Скопируйте `.env.example` в `.env` и заполните параметры:
   - `COLLECTION_OWNER` – владелец NFT‑коллекции;
   - `ADMIN_ADDRESS` – кошелёк администратора лотереи;
   - `TOKEN_ADDRESS` – jetton, используемый для оплаты билетов;
   - `TICKET_PRICE` – стоимость билета в jetton;
   - `REF_PERCENT` – процент для реферала (в б.п.).

## Деплой
1. Подготовьте метаданные `collection.json` и `0.json–7.json`, загрузите их в IPFS и укажите ссылку в `collectionConfig.content`.
2. Запустите `npm run start` и выберите `deployCollection` – будет создан контракт коллекции NFT.
3. Настройте `lotteryConfig` в `scripts/deployJet.ts` и выберите `deployJet` для развертывания лотереи.
4. Выполните `setLotteryAddress()` из того же скрипта, чтобы лотерея могла минтить NFT.

В `deployJet.ts` также доступны функции `withdraw()` и `finishRound(winner)`.

## Участие
Игрок отправляет jetton `transfer` на кошелёк лотереи с суммой `TICKET_PRICE`. При наличии реферала его адрес (267 бит) помещается в payload. Переплата возвращается, а указанная часть билета перечисляется рефереру.

## Механика лотереи
1. После получения платежа генерируется случайное число `x` от 0 до 11999.
2. При `x == 0` контракт уведомляет администратора о потенциальном джекпоте. Админ вызывает `finishRound(address)`:
   - игрок получает `JACKPOT_PRIZE` (10 000 jetton) и NFT `0`;
   - счётчик джекпота увеличивается и новые билеты не принимаются.
3. Иначе сравниваются диапазоны и лимиты выигрышей:
   - `x < 4` и `major < 3` – **Major** (1 800 jetton, NFT `1`)
   - `x < 14` и `high < 10` – **High** (700 jetton, NFT `2`)
   - `x < 64` и `mid < 50` – **Mid** (180 jetton, NFT `3`)
   - `x < 214` и `low_mid < 150` – **Low Mid** (50 jetton, NFT `4`)
   - `x < 514` и `low < 300` – **Low** (25 jetton, NFT `5`)
   - `x < 1714` и `mini < 1200` – **Mini** (10 jetton, NFT `6`)
   - иначе игрок получает NFT `7` без приза.
4. После выдачи призов соответствующие счётчики увеличиваются, NFT минтится из коллекции, а jetton переводится игроку.
5. На контракте должно оставаться не менее 0.05 TON и достаточный запас jetton для будущих выплат.
6. При отправке билета в `forward_payload` первым помещайте свой TON‑кошелёк, а после него при желании адрес реферала. В поле `forward_ton_amount` укажите не менее `0.27` TON.

## Пример отправки билета
```tsx
import { beginCell, Address } from '@ton/core';

const referral = undefined as string | undefined;

const forwardPayloadBuilder = beginCell().storeAddress(userWallet); // player wallet
if (referral) {
  forwardPayloadBuilder.storeAddress(Address.parse(referral));
}
const forwardPayload = forwardPayloadBuilder.endCell();

const payload = beginCell()
  .storeUint(0xf8a7ea5, 32)
  .storeUint(0, 64)
  .storeCoins(BigInt(process.env.TICKET_PRICE!))
  .storeAddress(Address.parse(lotteryWallet))
  .storeAddress(userWallet)
  .storeBit(0)
  .storeCoins(toNano('0.27'))
  .storeRef(forwardPayload)
  .endCell();
```

Отправьте `transfer` с этим payload через ваш кошелёк.
