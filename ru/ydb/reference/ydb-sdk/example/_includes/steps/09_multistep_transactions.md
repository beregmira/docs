---
sourcePath: core/reference/ydb-sdk/example/_includes/steps/09_multistep_transactions.md
---
## Многошаговые транзакции с промежуточной обработкой данных на стороне клиента {#multistep-transactions}

Фрагмент кода, приведенный ниже, демонстрирует возможность использования многошаговых транзакций, состоящих из нескольких запросов. Между выполнением запросов допустимо выполнение работы кода клиентского приложения.