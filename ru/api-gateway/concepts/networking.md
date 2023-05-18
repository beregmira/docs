# Сетевое взаимодействие в {{ api-gw-name }}

По умолчанию API-шлюз находится в изолированной IPv4-сети с включенным [NAT-шлюзом](../../vpc/concepts/gateways.md). Поэтому из него доступны только публичные IPv4-адреса.

## Пользовательская сеть

{% include [note-preview](../../_includes/note-preview.md) %}

Если необходимо, в настройках API-шлюза можно указать [облачную сеть](../../vpc/concepts/network.md#network). Тогда он будет:

* находиться в указанной облачной сети.
* иметь доступ не только в интернет, но и к пользовательским ресурсам, которые находятся в указанной сети, например базам данным, виртуальным машинам и т.п.
* иметь IP-адрес в диапазоне `198.19.0.0/16` при доступе к пользовательским ресурсам.

{% include [network](../../_includes/functions/network.md) %}