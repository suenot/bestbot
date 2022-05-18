# Торговая система "bestbot"
Данная сборка является примером торгового робота в рамках конкурса Тинькоф Инвестиций

# Зависимости
Данная сборка была протестирована в ОС Debian 11 и в NodeJs 16, но предполагается, что она будет работать и в других системах с установленным nodejs.

# Установка
`git clone git@github.com:betslus1/bestbot.git`

# Настройка системы
Настройка системы осуществляется в файле options.js

В первую очередь необходимо прописать:
- token от системы Тинькоф Инвестиций.
- tradeAccount Номер аккаунта, где будут проходить торги
- Логин/Пароль для доступа к системе
- Токен для уведомления в telegram а так же ID вашего аккаунта (Подробности в файле настроек)
- Прочие настройки задокументированы в файле options.js (Так же вы можете добавлять свои настройки)

# Запуск торговой системы
`node app.js`

# Функциональные возможности
- Получение/обработка Market-data в реальном времени по протоколу GRPC с серверов Тинькоф Инвестиций, в том числе стаканов/свечей/служебных сообщений
- Контроль балансов и заявок
- Возможность работы в различных торговых контурах (Реальный счет, Тестовый счет, Симуляция)
- Обработка ошибок, логирование работы торговой системы на каждом этапе, возможность просмаривать логи в удобном виде
- Уведомление в Telegram об различных событиях и ошибках
- Учет вашх сделок по методу FIFO, а так же рассчет сводной информации по инструментам.
- Возможность контролировать работу торговой системы через визуальный интерфейс
- Возможность интеграции собственных торговых стратегий и их тестирование

# Торговая стратегия
### Примеры торговых стратегий
- Стратегия Random: "Покупаем в случайное время, Продаем в случайное время". Данная торговая стратегия разработана для тестирования системы (максимилизация количества сделок)
- Стратегия RSI: Пример торговой стратегии, в которой робот принимает решение о покупке и продаже на основе рассчетных индикаторов

### Разработка своей стратегии
 Входные параметры: 
  - lastCandles - последние свечи (глубина задается в настройках)
  - currentOrder - список текущих выставленных заявок
  - currentBalance - текущий баланс
  
 Выходные параметры: 
  - logs - логи работы стратегии
  - commands - список приказов к торговой системе о выставлении сделок
  - indicators - рассчетные индикаторы на основе которых принималось решение

Разработка:
 - Разместите свою торговую стратегию в папке strategy
 - Дополнительно можете указать описание индикаторов на основе которых принимаются решения
 - В вашей торговой стратегии должна быть основная функция обработки "Step". Функция Step принимает Входные параметры, проводит все нобходимые действия в рамках стратегии и возвращает в торговую систему принятое решение

Сокращенный пример торговой стратегии (Полноценный пример смотрите в примерах торговых стратегий)
```
module.exports.indicators = { 'RSI':{ 'min':0, 'max':100, 'buy':40, 'sell':60 }};
  
module.exports.step       = function (lastCandles, currentOrder, currentBalance) {
   let indicators['RSI'] = calc_RSI(lastCandles);
   if(indicators['RSI'] > module.exports.indicators['RSI']?.buy){
     commands.push({'type':'Buy', 'price':price.Buy, 'quantity': quantity.Buy});
     logs.push('Покупаем: ${price.Buy} x ${quantity.Buy}');
   }
   if(indicators['RSI'] < module.exports.indicators['RSI']?.sell){
     commands.push({'type':'Sell', 'price':price.Sell, 'quantity': quantity.sell});
     logs.push('Продаем: ${price.Buy} x ${quantity.Buy}');
   }
   return {logs, commands, indicators};
 }
```
