---
name: PhraseDrill API Contract
description: Согласование контракта API между PhraseDrillApp и PhraseDrillService — эндпоинты, DTO, коды ответов
---

Работай с **обоими** проектами: **PhraseDrillApp** и **PhraseDrillService**.

Проверяй и согласовывай контракт между фронтом и бэком: пути и методы эндпоинтов, формат тел запросов/ответов, коды ответов и формат ошибок. При изменениях API вноси правки в обоих проектах согласованно: сначала бэк (эндпоинт, DTO), затем фронт (URL, разбор ответа и ошибок).

При изменениях кода применяй соглашения из `.cursor/rules/PhraseDrillAppRules.mdc` и `.cursor/rules/PhraseDrillServiceRules.mdc`.
