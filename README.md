# hw_BB
<!-- Your report starts here! -->

Lead Auditors:

- xxxxxxx

# Table of Contents

- [Table of Contents](#table-of-contents)
- [Protocol Summary](#protocol-summary)
- [Disclaimer](#disclaimer)
- [Risk Classification](#risk-classification)
- [Audit Details](#audit-details)
  - [Scope](#scope)
- [Executive Summary](#executive-summary)
  - [Issues found](#issues-found)
- [Findings](#findings)
- [High](#high)
- [Medium](#medium)
- [Low](#low)
- [Informational](#informational)
- [Gas](#gas)

# Protocol Summary

Protocol does X, Y, Z

# Disclaimer

The YOUR_NAME_HERE team makes all effort to find as many vulnerabilities in the code in the given time period, but holds no responsibilities for the findings provided in this document. A security audit by the team is not an endorsement of the underlying business or product. The audit was time-boxed and the review of the code was solely on the security aspects of the Solidity implementation of the contracts.

# Risk Classification

|            |        | Impact |        |     |
| ---------- | ------ | ------ | ------ | --- |
|            |        | High   | Medium | Low |
|            | High   | H      | H/M    | M   |
| Likelihood | Medium | H/M    | M      | M/L |
|            | Low    | M      | M/L    | L   |

We use the [CodeHawks](https://docs.codehawks.com/hawks-auditors/how-to-evaluate-a-finding-severity) severity matrix to determine severity. See the documentation for more details.

All vulnerabilities discovered during the audit are classified based on their potential severity and have the following classification:

| Severity | Description                                                                                                                                                                                                                |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| High     | Bugs leading to assets theft, fund access locking, or any other loss of funds and bugs that can trigger a contract failure. Further recovery is possible only by manual modification of the contract state or replacement. |
| Medium   | Bugs that can break the intended contract logic or expose it to DoS attacks, but do not cause direct loss funds.                                                                                                           |
| Low      | Bugs that do not have a significant immediate impact and could be easily fixed.                                                                                                                                            |
| Gas      | Bugs that are tied to unnecessary wasted gas.                                                                                                                                                                              |

# Audit Details

## Scope

- KittenRaffle.sol

# Executive Summary

## Issues found

| Severity | # of Findings |
| -------- | ------------- |
| CRITICAL |      2        |
| HIGH     |      3        |
| MEDIUM   |      2        |
| LOW      |               |

# Findings

### VULNERABILITY_NAME

### Description

### Recommendation

### POC

# High
1) Уязвимость в функции selectWinner из-за использования случайного числа на основе блочного хеша

  Описание: Функция selectWinner использует хеш блока и другие параметры для генерации случайного числа для выбора победителя. Это может привести к предсказуемым результатам и уязвимостям атаки майнеров. Учитывая, что контракт использует block.timestamp и block.difficulty для генерации случайного числа, это действительно может создать проблемы безопасности, так как эти параметры могут быть предсказуемыми.

  Рекомендация: Использовать внешние источники случайности, такие как Chainlink VRF, для более надежной генерации случайных чисел.

2) Неверный выигрыш

  Описание: Если пользователь вернул свои средства и отказался участвовать в латерее, то totalAmountCollected = players.length * entranceFee; все равно будет расчитан с учетом этого пользователя и выигрышная сумма будет больше необходимой, тогда будут утеряны средства.

  Рекомендация: Определять сумму выигрыша не по длина массива players, а по фактуческому количествоу реальных адресов в списке.
  

# Medium

1) Переполнение массива

  Описание: Возможность добавления большого количества игроков в розыгрыш может привести к переполнению массива players, что может вызвать отказ контракта из-за нехватки газа или даже к аварийному завершению контракта.

  Рекомендации: Добавить проверку на максимальное количество элементов в массиве или использовать итерацию с for вместо for по индексам.


2) Отсутствие проверки успешности вызова функции sendValue в функции refund

  Описание: Результат вызова функции sendValue для отправки средств не проверяется на успешность, что может привести к потере средств победителя в случае неудачной операции.

  Рекомендация: Добавить проверку успешности вызова функции sendValue и соответствующую обработку ошибок.

3) Уязвимость DoS
  Описание: Метод _isActivePlayer() может стать целью атак DoS, если злоумышленник вызывает его с большим количеством активных игроков, что приведет к затрате большого количества газа.

  Рекомендации: Провести оценку затрат газа для данного метода и, если возможно, оптимизировать его для уменьшения затрат газа.

# Low

1) Отсутствие проверки наличия достаточного баланса контракта для выплаты призов.

  Описание: В функции selectWinner() происходит перевод средств на адрес победителя (winner.call{value: prizePool}("")), однако перед этим не выполняется проверка наличия достаточного баланса контракта для выполнения этой операции. В случае отсутствия достаточного баланса возникнет ошибка выполнения транзакции.

  Рекомендация: Рекомендуется добавить проверку баланса контракта перед выполнением операции перевода средств на адрес победителя в функции selectWinner(). Это позволит избежать ошибок выполнения транзакции из-за нехватки средств на балансе контракта.


2) Отсутствие проверки наличия списка игроков в функции enterRaffle

  Описание: В функции enterRaffle отсутствует проверка наличия списка новых игроков перед выполнением операций добавления их в массив players, что может привести к неожиданным результатам, если функция вызывается с пустым списком.

  Рекомендация: Рекомендуется добавить проверку наличия списка новых игроков перед выполнением операций добавления их в массив.


# Gas
