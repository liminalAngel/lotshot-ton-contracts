1) После того как репозиторий был склонирован, необходимо установить зависимости
npm install

2) Заполнить файл .env.example и переименовать его в .env

3) Для создания коллекции нужно иметь на ссылку на метаданные, например "ipfs://bafybeihvz6ausl63mi5mrnutke7invvcj57qq4brh2ple6jrf4aiouvrbe"
По ссылке должна открываться папка с доступными для чтения файлами collection.json, 0.json - 7.json

4) Чтобы развернуть контракт коллекции в блокчейне нужно заполнить collectionConfig в файле scripts/deployJet.ts
owner - адрес владельца коллекции
royalty - указывается процент роялти
content - ссылка на метаданные

- ввести команду:
npm run start

- далее выбирать:
deployCollection
mainnet
Mnemonic

5) После развертывания контракта коллекции можно приступить к развертыванию контракта лотереи
для этого нужно заполнить lotteryConfig в scripts/deployJet.ts

collectionAddress - будет выставлен автоматически, адрес формируется из collectionConfig
adminAddress - указать кошелек администратора лотереи
price - указываем цену лотерейного билета
refPercent - процент комиссии для реферала в б.п.

6) в файле deployJet.ts описаны функции для развертывания и взаимодействия с контрактом лотереи

deploy() - отправляет транзакция для развертывания контракта
setLotteryAddress() - отправляет транзакцию на адрес коллекции для установки lottery_address
withdraw() - выводит деньги с контракта лотереи (оставляет 0.05)
finishRound() - закрывает контракт лотереи и отправляет нфт для победителя jackpot'а

вызовы функций закомментированы, для отправки транзакции нужно раскомментировать нужную и ввести:
npm run start

выбрать:
deployJet
mainnet
Mnemonic

чтобы лотерея работала необходимо обязательно отправить транзакцию на deploy() и setLotteryAddress()



1) Once the repository has been cloned, you need to install the dependencies
npm install

2) Fill in the .env.example file and rename it to .env

3) To create a collection you need to have a metadata link to the collection, for example “ipfs://bafybeihvvz6ausl63mi5mrnutke7invvcj57qqq4brh2ple6jrf4aiouvrbe”
The link should open a folder with readable files collection.json, 0.json - 7.json

4) To deploy the collection contract in the blockchain you need to fill the collectionConfig in the scripts/deployJet.ts file
owner - address of the collection owner
royalty - specifies the royalty percentage
content - reference to metadata

- enter the command:
npm run start

- then select:
deployCollection
mainnet
Mnemonic

5) Once the collection contract is deployed, you can start deploying the lottery contract
to do this you need to fill in lotteryConfig in scripts/deployJet.ts

collectionAddress - will be set automatically, the address is generated from collectionConfig
adminAddress - specify the lottery administrator's wallet
price - specify the price of the lottery ticket
refPercent - commission for the referral in basis points

6) the deployJet.ts file describes functions for deployment and interaction with the lottery contract

deploy() - sends a transaction to deploy the contract
setLotteryAddress() - sends a transaction to the collection address to set lottery_address
withdraw() - withdraws money from the lottery contract (leaves 0.05)
finishRound() - closes the lottery contract and sends nft for the jackpot winner

function calls are commented out, to send a transaction you need to uncomment the necessary one and enter it:
npm run start

select:
deployJet
mainnet
Mnemonic

for the lottery to work you must send a transaction to deploy() and setLotteryAddress().

### Participation

To buy a ticket without a referral, simply send the ticket price to the lottery contract with an empty body. If you have a referrer, put their address (267 bits) in the message body when sending the payment. A part of the ticket value, configured during deployment, will be transferred to the referrer automatically.

Translated with DeepL.com (free version)