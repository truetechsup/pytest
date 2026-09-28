# Pytest + Test IT + GitHub Actions

Пример автотестов на Pytest, которые запускаются из Test IT через GitHub Actions и отправляют результаты обратно в Test IT с помощью [testit-adapter-pytest](https://github.com/testit-tms/adapters-python/tree/main/testit-adapter-pytest).

**Проект в Test IT:** [team-0tm5.testit.software/projects/42/autotests](https://team-0tm5.testit.software/projects/42/autotests)

## Как это работает

1. Test IT отправляет webhook в GitHub — событие `repository_dispatch` с типом `run-tests`.
2. Запускается workflow [.github/workflows/.github-ci.yml](.github/workflows/.github-ci.yml).
3. Тесты выполняются через `pytest --testit`, результаты загружаются в Test IT.
4. К прогону в Test IT прикрепляется ссылка на пайплайн GitHub Actions (`actions/runs/<run_id>`).

### Режимы запуска

Режим задаётся полем `adapter_mode` в webhook.

| `adapter_mode` | Что происходит | Имя прогона |
|---|---|---|
| `0` | Результаты пишутся в существующий прогон, `test_run_id` берётся из webhook. Sync-storage запускается в workflow. | `GitHub Actions (adapterMode=0)` |
| `2` | Адаптер сам создаёт новый прогон, `test_run_id` не передаётся. | `GitHub Actions (adapterMode=2)` |

### Данные из webhook

```json
{
  "event_type": "run-tests",
  "client_payload": {
    "adapter_mode": "0",
    "url": "https://team-0tm5.testit.software",
    "project_id": "<id проекта>",
    "configuration_id": ["<id конфигурации>"],
    "test_run_id": "<id прогона, только для adapter_mode=0>"
  }
}
```

### Секреты репозитория

| Секрет | Назначение |
|---|---|
| `TMS_PRIVATE_TOKEN` | Приватный токен пользователя Test IT |

## Структура проекта

* **.github/workflows/.github-ci.yml** – workflow запуска тестов по webhook из Test IT
* **tests/** – тесты
    * **test_annotations.py** – примеры [аннотаций testit-adapter-pytest](https://github.com/testit-tms/adapters-python/tree/main/testit-adapter-pytest#decorators)
    * **test_dependency.py** – примеры с [pytest-dependency](https://pytest-dependency.readthedocs.io/en/stable/usage.html#using-pytest-dependency)
    * **test_methods.py** – примеры [методов testit-adapter-pytest](https://github.com/testit-tms/adapters-python/tree/main/testit-adapter-pytest#decorators)
    * **test_steps.py** – примеры [шагов testit-adapter-pytest](https://github.com/testit-tms/adapters-python/tree/main/testit-adapter-pytest#decorators)
